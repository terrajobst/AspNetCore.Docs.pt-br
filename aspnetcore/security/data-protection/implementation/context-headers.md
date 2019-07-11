---
title: Cabeçalhos de contexto no ASP.NET Core
author: rick-anderson
description: Conheça os detalhes de implementação dos cabeçalhos de contexto da proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 518423f5df93924d3df144994e4beb1755cd0bfc
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814021"
---
# <a name="context-headers-in-aspnet-core"></a>Cabeçalhos de contexto no ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Plano de fundo e a teoria

No sistema de proteção de dados, uma "chave" significa autenticado de um objeto que pode fornecer serviços de criptografia. Cada chave é identificado por uma id exclusiva (um GUID), e ela transmite informações algorítmicos e material entropic. Ele destina-se que cada chave carregam entropia exclusiva, mas o sistema não pode impor que e estamos também precisa levar em conta para os desenvolvedores que podem alterar o anel de chave manualmente, modificando as informações de algoritmos de uma chave existente do anel de chave. Para atingir nossos requisitos de segurança fornecidos nesses casos, o sistema de proteção de dados tem um conceito de [agilidade criptográfica](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), que permite que com segurança usando um único valor entropic entre vários algoritmos de criptografia.

A maioria dos sistemas que dão suporte a agilidade criptográfica de fazer isso, incluindo algumas informações de identificação sobre o algoritmo de dentro do payload. OID do algoritmo geralmente é um bom candidato para isso. No entanto, um problema que tivemos é que há várias maneiras para especificar o mesmo algoritmo: "AES" (CNG) e o gerenciado Aes, AesManaged, AesCryptoServiceProvider, AesCng e RijndaelManaged (determinados parâmetros de específicos) classes são, na verdade, tudo a mesma coisa, e seria preciso manter um mapeamento de tudo isso para o OID correto. Se um desenvolvedor quiser fornecer um algoritmo personalizado (ou até mesmo outra implementação do AES!), eles teria Conte-nos sua OID. Esta etapa de registro extras torna a configuração do sistema particularmente doloroso.

Voltar em etapas, decidimos que podemos foram se aproximando o problema na direção errada. Um OID informa o que é o algoritmo, mas nós não nos importamos realmente sobre isso. Se é necessário usar um único valor entropic com segurança em dois algoritmos diferentes, não é necessário para que possamos saber o que os algoritmos realmente são. O que realmente nos interessa é como eles se comportam. Qualquer algoritmo de criptografia de bloco simétricas decente também é uma permutação de números pseudoaleatório forte (PRP): corrija as entradas (chave, texto sem formatação do modo, IV, o encadeamento) e a saída de texto cifrado com grandes probabilidade será distinta de qualquer outra codificação de bloco simétricas considerando as mesmas entradas de algoritmo. Da mesma forma, qualquer função de hash com chave decente também é uma forte pseudoaleatório PRF (função), e dado um conjunto fixo de entrada sua saída predominantemente será distinta de qualquer outra função de hash com chave.

Podemos usar esse conceito de alta segurança PRPs e PRFs para criar um cabeçalho de contexto. Esse cabeçalho de contexto atua essencialmente como uma impressão digital do estável sobre os algoritmos em uso para qualquer operação determinada, e fornece a agilidade criptográfica necessária para o sistema de proteção de dados. Esse cabeçalho pode ser reproduzido e é usado posteriormente como parte do [processo de derivação de subchaves](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Há duas maneiras diferentes para criar o cabeçalho de contexto, dependendo dos modos de operação dos algoritmos subjacentes.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Criptografia do modo CBC + autenticação HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

O cabeçalho de contexto consiste dos seguintes componentes:

* [16 bits] O valor 00 00, que é um marcador que significa "criptografia CBC + autenticação HMAC".

* [32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas.

* [32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas.

* [32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo HMAC. (Atualmente, o tamanho da chave sempre corresponde o tamanho do resumo.)

* [32 bits] O tamanho (em bytes, big-endian) resumo do algoritmo HMAC.

* EncCBC (K_E, IV, ""), que é a saída do algoritmo de criptografia de bloco simétricas dada uma entrada de cadeia de caracteres vazia e onde o IV é um vetor de zeros. A construção de K_E é descrita abaixo.

* MAC (K_H, ""), que é a saída do algoritmo HMAC dada uma entrada de cadeia de caracteres vazia. A construção de K_H é descrita abaixo.

O ideal é que podemos passar para K_E e K_H vetores de zeros. No entanto, queremos evitar a situação em que o algoritmo subjacente verifica a existência de chaves fracas antes de executar qualquer operação (especialmente DES e 3DES), que impede o usando um padrão simple ou repetitivos, como um vetor de zeros.

Em vez disso, usamos o NIST SP800 108 KDF no modo de contador (consulte [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 5.1 seg.) com uma chave de comprimento zero, o rótulo e o contexto e o HMACSHA512 como o PRF subjacente. Derivamos | K_E | + | K_H | bytes de saída, em seguida, decompor o resultado em K_E e K_H em si. Matematicamente, isso é representado da seguinte maneira.

( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Exemplo: AES-192-CBC + HMACSHA256

Por exemplo, considere o caso em que o algoritmo de criptografia de bloco simétricas é AES-192-CBC e o algoritmo de validação é HMACSHA256. O sistema geraria o cabeçalho de contexto usando as etapas a seguir.

Primeiro, permita (K_E | | K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", rótulo = "", contexto = ""), onde | K_E | = 192 bits e | K_H | = 256 bits por algoritmos especificados. Isso leva a K_E = 5BB6... 21DD e K_H = A04A... 00A9 no exemplo a seguir:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Em seguida, computação Enc_CBC (K_E, IV, "") para o AES-192-CBC dado IV = 0 * e K_E como acima.

result := F474B1872B3B53E4721DE19C0841DB6F

Em seguida, o MAC de computação (K_H, "") para HMACSHA256 determinado K_H como acima.

result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Isso produz o cabeçalho de contexto completo abaixo:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Esse cabeçalho de contexto é a impressão digital do par de algoritmo de criptografia autenticada (criptografia AES-192-CBC + HMACSHA256 validação). Os componentes, como descrito [acima](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) são:

* o marcador (00 00)

* o comprimento da chave de criptografia de bloco (00 00 00 18)

* o tamanho de bloco de codificação de bloco (00 00 00 10)

* o comprimento da chave HMAC (00 00 00 20)

* o tamanho do resumo HMAC (00 00 00 20)

* o bloco cifrado saída PRP (F4 74 - DB 6F) e

* a saída de HMAC PRF (D4 79 - end).

> [!NOTE]
> A criptografia do modo CBC + HMAC cabeçalho de contexto de autenticação baseia-se da mesma forma independentemente se as implementações de algoritmos são fornecidas pelo CNG do Windows ou por tipos SymmetricAlgorithm e KeyedHashAlgorithm gerenciados. Isso permite que aplicativos executados em diferentes sistemas operacionais para produzir confiavelmente o mesmo cabeçalho de contexto, embora as implementações de algoritmos diferem entre sistemas operacionais. (Na prática, o KeyedHashAlgorithm não precisa ser um HMAC adequado. Ele pode ser qualquer tipo de algoritmo de hash com chave.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Exemplo: 3DES-192-CBC + HMACSHA1

Primeiro, permita (K_E | | K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", rótulo = "", contexto = ""), onde | K_E | = 192 bits e | K_H | = 160 bits por algoritmos especificados. Isso leva a K_E = A219... E2BB e K_H = DC4A... B464 no exemplo a seguir:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Em seguida, computação Enc_CBC (K_E, IV, "") para 3DES-192-CBC dado IV = 0 * e K_E como acima.

resultado: = ABB100F81E53E10E

Em seguida, o MAC de computação (K_H, "") para HMACSHA1 determinado K_H como acima.

result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Isso produz o cabeçalho de contexto total que é uma impressão digital do autenticado par de algoritmo do encryption (criptografia 3DES-192-CBC + validação HMACSHA1), mostrado abaixo:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Os componentes são discriminados da seguinte maneira:

* o marcador (00 00)

* o comprimento da chave de criptografia de bloco (00 00 00 18)

* o tamanho de bloco de codificação de bloco (00 00 00 08)

* o comprimento da chave HMAC (00 00 00 14)

* o tamanho do resumo HMAC (00 00 00 14)

* o bloco cifrado saída PRP (B1 AB - E1 0E) e

* a saída de HMAC PRF (76 EB - end).

## <a name="galoiscounter-mode-encryption--authentication"></a>Criptografia de modo Galois/contador + autenticação

O cabeçalho de contexto consiste dos seguintes componentes:

* [16 bits] O valor 00 01, o que é um marcador que significa "criptografia GCM + autenticação".

* [32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas.

* [32 bits] O tamanho nonce (em bytes, big-endian) usado durante as operações de criptografia autenticada. (Para nosso sistema, isso foi corrigido no tamanho de nonce = 96 bits.)

* [32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas. (Para GCM, isso foi corrigido no tamanho do bloco de = 128 bits.)

* [32 bits] O autenticação marca tamanho (em bytes, big-endian) produzido pela função criptografia autenticada. (Para nosso sistema, isso foi corrigido no tamanho da marca = 128 bits.)

* [128 bits] A marca de Enc_GCM (K_E, nonce, ""), que é a saída do algoritmo de criptografia de bloco simétricas dada uma entrada de cadeia de caracteres vazia e onde o nonce é um vetor de zeros de 96 bits.

K_E é derivado usando o mesmo mecanismo como a criptografia CBC + o cenário de autenticação do HMAC. No entanto, já que não há nenhum K_H em jogo aqui, essencialmente, temos | K_H | = 0, e o algoritmo se recolhe para o abaixo do formulário.

K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-256-gcm"></a>Exemplo: AES-256-GCM

Primeiro, permita K_E = SP800_108_CTR (prf = HMACSHA512, key = "", rótulo = "", contexto = ""), onde | K_E | = 256 bits.

K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

Em seguida, a marca de autenticação de Enc_GCM de computação (K_E, nonce, "") para AES-256-GCM dado nonce = 096 e K_E como acima.

result := E7DCCE66DF855A323A6BB7BD7A59BE45

Isso produz o cabeçalho de contexto completo abaixo:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Os componentes são discriminados da seguinte maneira:

* o marcador (00 01)

* o comprimento da chave de criptografia de bloco (00 00 00 20)

* o tamanho de nonce (00 00 00 0 C)

* o tamanho de bloco de codificação de bloco (00 00 00 10)

* o tamanho da marca de autenticação (00 00 00 10) e

* a marca de autenticação executem a codificação de bloco (controlador de domínio E7 - end).
