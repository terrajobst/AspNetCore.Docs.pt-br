---
title: Derivação de subchave e criptografia autenticada no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação de derivação de subchave de proteção de dados ASP.NET Core e criptografia autenticada.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: bbfde378755b09cd5b1217b8cf66249b9fa1d6ad
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660548"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Derivação de subchave e criptografia autenticada no ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

A maioria das chaves no anel de chave conterá alguma forma de entropia e terá informações de algoritmos que informam "criptografia do modo CBC + validação HMAC" ou "GCM criptografia + validação". Nesses casos, nos referimos à entropia incorporada como o material de chaveamento mestre (ou KM) para essa chave, e executamos uma função de derivação de chave para derivar as chaves que serão usadas para as operações de criptografia reais.

> [!NOTE]
> As chaves são abstratas e uma implementação personalizada pode não se comportar como a seguir. Se a chave fornecer sua própria implementação de `IAuthenticatedEncryptor` em vez de usar uma de nossas fábricas internas, o mecanismo descrito nesta seção não se aplicará mais.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Dados autenticados adicionais e derivação de subchave

A interface `IAuthenticatedEncryptor` serve como interface principal para todas as operações de criptografia autenticadas. Seu método de `Encrypt` usa dois buffers: texto não criptografado e additionalAuthenticatedData (AAD). O fluxo de conteúdo de texto sem formatação inalterou a chamada para `IDataProtector.Protect`, mas o AAD é gerado pelo sistema e consiste em três componentes:

1. O cabeçalho mágico de 32 bits 09 F0 C9 F0 que identifica esta versão do sistema de proteção de dados.

2. A ID da chave de 128 bits.

3. Uma cadeia de caracteres de comprimento variável formada pela cadeia de finalidade que criou o `IDataProtector` que está executando esta operação.

Como o AAD é exclusivo para a tupla de todos os três componentes, podemos usá-lo para derivar novas chaves de KM em vez de usar KM em todas as nossas operações de criptografia. Para cada chamada para `IAuthenticatedEncryptor.Encrypt`, ocorre o seguinte processo de derivação de chave:

( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)

Aqui, estamos chamando o NIST SP800-108 KDF no modo de contador (consulte [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) com os seguintes parâmetros:

* Chave de derivação de chave (KDK) = K_M

* PRF = HMACSHA512

* rótulo = additionalAuthenticatedData

* context = contextHeader || keyModifier

O cabeçalho de contexto é de comprimento variável e, essencialmente, serve como uma impressão digital dos algoritmos para os quais estamos derivando K_E e K_H. O modificador de chave é uma cadeia de caracteres de 128 bits gerada aleatoriamente para cada chamada para `Encrypt` e serve para garantir uma grande probabilidade de que KE e KH sejam exclusivos para essa operação de criptografia de autenticação específica, mesmo se todas as outras entradas para o KDF forem constantes.

Para criptografia do modo CBC + operações de validação HMAC, | K_E | é o comprimento da chave de codificação do bloco simétrico e | K_H | é o tamanho de resumo da rotina HMAC. Para operações de validação e criptografia do GCM, | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>Criptografia de modo CBC + validação HMAC

Depois que K_E é gerado por meio do mecanismo acima, geramos um vetor de inicialização aleatória e executamos o algoritmo de codificação de bloco simétrico para codificar o texto não criptografado. O vetor de inicialização e o texto cifrado são executados por meio da rotina HMAC inicializada com a chave K_H para produzir o MAC. Esse processo e o valor de retorno são representados graficamente abaixo.

![Modo CBC-processar e retornar](subkeyderivation/_static/cbcprocess.png)

*saída: = keymodifier | | IV | | E_cbc (K_E, IV, dados) | | HMAC (K_H, IV | | E_cbc (K_E, IV, dados))*

> [!NOTE]
> A implementação de `IDataProtector.Protect` [precederá o cabeçalho mágico e a ID de chave](xref:security/data-protection/implementation/authenticated-encryption-details) para saída antes de devolvê-lo ao chamador. Como o cabeçalho mágico e a ID da chave são implicitamente parte do [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), e como o modificador de chave é alimentado como entrada para o KDF, isso significa que cada byte de carga final retornado é autenticado pelo Mac.

## <a name="galoiscounter-mode-encryption--validation"></a>Criptografia do modo de Galois/contador + validação

Uma vez que K_E é gerado por meio do mecanismo acima, geramos um nonce aleatório de 96 bits e executamos o algoritmo de codificação de bloco simétrico para codificar o texto não criptografado e produzir a marca de autenticação de 128 bits.

![Processo e retorno do modo GCM](subkeyderivation/_static/galoisprocess.png)

*saída: = keymodifier | | Nonce | | E_gcm (K_E, nonce, dados) | | authTag*

> [!NOTE]
> Embora o GCM nativamente dê suporte ao conceito de AAD, ainda estamos alimentando o AAD somente para o KDF original, optando por passar uma cadeia de caracteres vazia para o seu parâmetro AAD. A razão para isso é de duas dobras. Primeiro, [para dar suporte à agilidade](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) , nunca queremos usar K_M diretamente como a chave de criptografia. Além disso, o GCM impõe requisitos de exclusividade muito estritos em suas entradas. A probabilidade de que a rotina de criptografia do GCM seja invocada em dois ou mais conjuntos distintos de dados de entrada com o mesmo par (chave, nonce) não deve exceder 2 ^ 32. Se corrigirmos K_E não é possível executar mais de 2 ^ 32 operações de criptografia antes de executarmos afoul do limite de 2 ^-32. Isso pode parecer um número muito grande de operações, mas um servidor Web de tráfego alto pode passar por 4.000.000.000 solicitações em alguns dias, bem dentro do tempo de vida normal dessas chaves. Para permanecer em conformidade com o limite de probabilidade de 2 ^-32, continuamos a usar um modificador de chave de 128 bits e um nonce de 96 bits, que estende radicalmente a contagem de operação utilizável para qualquer K_M determinado. Para simplificar o design, compartilhamos o caminho de código KDF entre as operações CBC e GCM, e como o AAD já é considerado no KDF, não há necessidade de encaminhá-lo à rotina do GCM.
