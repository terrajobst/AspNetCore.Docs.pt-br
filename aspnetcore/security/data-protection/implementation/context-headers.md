---
title: Cabeçalhos de contexto em ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação de cabeçalhos de contexto de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 518423f5df93924d3df144994e4beb1755cd0bfc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666575"
---
# <a name="context-headers-in-aspnet-core"></a>Cabeçalhos de contexto em ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Plano de fundo e teoria

No sistema de proteção de dados, uma "chave" significa um objeto que pode fornecer serviços de criptografia autenticados. Cada chave é identificada por uma ID exclusiva (um GUID) e contém informações de algoritmos de ti e material de Entropic. É pretendido que cada chave tenha uma entropia exclusiva, mas o sistema não possa impor isso e também precisamos considerar os desenvolvedores que podem alterar o anel de chave manualmente modificando as informações de algoritmo de uma chave existente no anel de chave. Para atingir nossos requisitos de segurança, considerando esses casos, o sistema de proteção de dados tem um conceito de [agilidade de criptografia](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), que permite usar com segurança um único valor de Entropic em vários algoritmos criptográficos.

A maioria dos sistemas que dão suporte à agilidade de criptografia faz isso incluindo algumas informações de identificação sobre o algoritmo dentro da carga. O OID do algoritmo geralmente é um bom candidato para isso. No entanto, um problema que encontramos é que há várias maneiras de especificar o mesmo algoritmo: "AES" (CNG) e as classes AES, AesManaged, AesCryptoServiceProvider, AesCng e RijndaelManaged (dadas determinados parâmetros específicos) são realmente as mesmas e precisaremos manter um mapeamento de todos eles para o OID correto. Se um desenvolvedor quisesse fornecer um algoritmo personalizado (ou até mesmo outra implementação do AES!), ele teria que nos dizer seu OID. Essa etapa de registro extra torna a configuração do sistema particularmente difícil.

Voltando, decidimos que estamos se aproximando do problema da direção errada. Um OID informa o que é o algoritmo, mas não nos preocupamos com isso. Se precisarmos usar um único valor Entropic com segurança em dois algoritmos diferentes, não é necessário que possamos saber quais são os algoritmos realmente. O que realmente nos preocupamos é como eles se comportam. Qualquer algoritmo de codificação de bloco simétrico claro é também uma pseudoaleatória (PRP) de alta segurança: corrija as entradas (chave, modo de encadeamento, IV, texto não criptografado) e a saída de texto cifrado com probabilidade impressionante será diferente de qualquer outra codificação de bloco simétrico algoritmo de acordo com as mesmas entradas. Da mesma forma, qualquer função de hash com chave de certo é também uma função de pseudoaleatória forte (PRF) e, dada uma entrada fixa definida, sua saída será intensamente distinta de qualquer outra função de hash com chave.

Usamos esse conceito de PRPs e PRFs fortes para criar um cabeçalho de contexto. Esse cabeçalho de contexto essencialmente atua como uma impressão digital estável nos algoritmos em uso para qualquer operação específica e fornece a agilidade criptográfica necessária para o sistema de proteção de dados. Esse cabeçalho é reproduzível e usado posteriormente como parte do processo de [derivação da subchave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Há duas maneiras diferentes de criar o cabeçalho de contexto dependendo dos modos de operação dos algoritmos subjacentes.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Criptografia de modo CBC + autenticação HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

O cabeçalho de contexto consiste nos seguintes componentes:

* [16 bits] O valor 00 00, que é um marcador que significa "criptografia de CBC + autenticação HMAC".

* [32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de codificação de bloco simétrico.

* [32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de codificação de bloco simétrico.

* [32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo HMAC. (Atualmente, o tamanho da chave sempre corresponde ao tamanho do resumo.)

* [32 bits] O tamanho de resumo (em bytes, big-endian) do algoritmo HMAC.

* EncCBC (K_E, IV, ""), que é a saída do algoritmo de codificação de bloco simétrico, dada uma entrada de cadeia de caracteres vazia e em que o IV é um vetor de todos os zero. A construção de K_E é descrita abaixo.

* MAC (K_H, ""), que é a saída do algoritmo HMAC, dada uma entrada de cadeia de caracteres vazia. A construção de K_H é descrita abaixo.

Idealmente, poderíamos passar todos os vetores de zero para K_E e K_H. No entanto, desejamos evitar a situação em que o algoritmo subjacente verifica a existência de chaves fracas antes de executar qualquer operação (notavelmente DES e 3DES), que impede o uso de um padrão simples ou repetível, como um vetor de todos os zeros.

Em vez disso, usamos o NIST SP800-108 KDF no modo de contador (consulte [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) com uma chave de comprimento zero, rótulo e contexto e HMACSHA512 como o PRF subjacente. Derivamos | K_E | + | K_H | bytes de saída e, em seguida, decompor o resultado em K_E e K_H em si. Matematicamente, isso é representado da seguinte maneira.

( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Exemplo: AES-192-CBC + HMACSHA256

Como exemplo, considere o caso em que o algoritmo de codificação de bloco simétrico é AES-192-CBC e o algoritmo de validação é HMACSHA256. O sistema geraria o cabeçalho de contexto usando as etapas a seguir.

Primeiro, Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, chave = "", rótulo = "", contexto = ""), em que | K_E | = 192 bits e | K_H | = 256 bits de acordo com os algoritmos especificados. Isso leva a K_E = 5BB6.. 21DD e K_H = A04A.. 00A9 no exemplo abaixo:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Em seguida, COMPUTE Enc_CBC (K_E, IV, "") para AES-192-CBC dado IV = 0 * e K_E como acima.

result := F474B1872B3B53E4721DE19C0841DB6F

Em seguida, COMPUTE MAC (K_H, "") para HMACSHA256 fornecido K_H como acima.

result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Isso produz o cabeçalho de contexto completo abaixo:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Esse cabeçalho de contexto é a impressão digital do par de algoritmos de criptografia autenticado (AES-192-CBC criptografia + validação de HMACSHA256). Os componentes, conforme descrito [acima](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , são:

* o marcador (00 00)

* o comprimento da chave de codificação do bloco (00 00 00 18)

* o tamanho do bloco de codificação de bloco (00 00 00 10)

* o comprimento da chave HMAC (00 00 00 20)

* o tamanho do HMAC Digest (00 00 00 20)

* a saída bloquear PRP de codificação (F4 74-DB 6F) e

* a saída de HMAC PRF (D4 79-end).

> [!NOTE]
> O cabeçalho de contexto de autenticação de criptografia do modo CBC + HMAC é criado da mesma maneira, independentemente de as implementações de algoritmos serem fornecidas pelo Windows CNG ou por tipos SymmetricAlgorithm e KeyedHashAlgorithm gerenciados. Isso permite que os aplicativos executados em diferentes sistemas operacionais produzam o mesmo cabeçalho de contexto de forma confiável, mesmo que as implementações dos algoritmos sejam diferentes entre os sistemas operacionais. (Na prática, o KeyedHashAlgorithm não precisa ser um HMAC adequado. Pode ser qualquer tipo de algoritmo de hash com chave.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Exemplo: 3DES-192-CBC + HMACSHA1

Primeiro, Let (K_E | | K_H) = SP800_108_CTR (PRF = HMACSHA512, chave = "", rótulo = "", contexto = ""), em que | K_E | = 192 bits e | K_H | = 160 bits de acordo com os algoritmos especificados. Isso leva a K_E = A219.. E2BB e K_H = DC4A.. B464 no exemplo abaixo:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Em seguida, COMPUTE Enc_CBC (K_E, IV, "") para 3DES-192-CBC dado IV = 0 * e K_E como acima.

resultado: = ABB100F81E53E10E

Em seguida, COMPUTE MAC (K_H, "") para HMACSHA1 fornecido K_H como acima.

result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Isso produz o cabeçalho de contexto completo, que é uma impressão digital do par de algoritmos de criptografia autenticado (3DES-192-CBC criptografia + validação de HMACSHA1), mostrado abaixo:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Os componentes são divididos da seguinte maneira:

* o marcador (00 00)

* o comprimento da chave de codificação do bloco (00 00 00 18)

* o tamanho do bloco de codificação de bloco (00 00 00 08)

* o comprimento da chave HMAC (00 00 00 14)

* o tamanho do HMAC Digest (00 00 00 14)

* a saída bloquear PRP de codificação (AB B1-E1 0E) e

* a saída de HMAC PRF (76 EB-end).

## <a name="galoiscounter-mode-encryption--authentication"></a>Criptografia do modo de Galois/contador + autenticação

O cabeçalho de contexto consiste nos seguintes componentes:

* [16 bits] O valor 00 01, que é um marcador que significa "criptografia GCM + autenticação".

* [32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de codificação de bloco simétrico.

* [32 bits] O tamanho de nonce (em bytes, big-endian) usado durante operações de criptografia autenticadas. (Para nosso sistema, isso é corrigido em um tamanho nonce = 96 bits.)

* [32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de codificação de bloco simétrico. (Para GCM, isso é corrigido no tamanho do bloco = 128 bits.)

* [32 bits] O tamanho da marca de autenticação (em bytes, big-endian) produzida pela função de criptografia autenticada. (Para nosso sistema, isso é fixo no tamanho da marca = 128 bits.)

* [128 bits] A marca de Enc_GCM (K_E, nonce, ""), que é a saída do algoritmo de codificação de bloco simétrico, dada uma entrada de cadeia de caracteres vazia e em que o nonce é um vetor de todos-zero de 96 bits.

K_E é derivado usando o mesmo mecanismo do cenário de autenticação CBC Encryption + HMAC. No entanto, como não há K_H em jogo aqui, nós basicamente temos | K_H | = 0, e o algoritmo é recolhido para o formulário abaixo.

K_E = SP800_108_CTR (PRF = HMACSHA512, chave = "", rótulo = "", contexto = "")

### <a name="example-aes-256-gcm"></a>Exemplo: AES-256-GCM

Primeiro, permita K_E = SP800_108_CTR (PRF = HMACSHA512, chave = "", rótulo = "", contexto = ""), em que | K_E | = 256 bits.

K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

Em seguida, Compute a marca de autenticação de Enc_GCM (K_E, nonce, "") para AES-256-GCM dado nonce = 096 e K_E como acima.

result := E7DCCE66DF855A323A6BB7BD7A59BE45

Isso produz o cabeçalho de contexto completo abaixo:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Os componentes são divididos da seguinte maneira:

* o marcador (00 01)

* o comprimento da chave de codificação do bloco (00 00 00 20)

* o tamanho de nonce (00 00 00 0C)

* o tamanho do bloco de codificação de bloco (00 00 00 10)

* o tamanho da marca de autenticação (00 00 00 10) e

* a marca de autenticação da execução do bloco cipher (E7 DC-end).
