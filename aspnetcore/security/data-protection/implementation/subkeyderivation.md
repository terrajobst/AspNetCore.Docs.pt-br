---
title: Derivação de subchaves e criptografia autenticada no ASP.NET Core
author: rick-anderson
description: Saiba mais detalhes de implementação da proteção de dados do ASP.NET Core subchave derivação e autenticados de criptografia.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: 37e7b01700e8a6b755b5ed16a9d7d75a9eeb970e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891833"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Derivação de subchaves e criptografia autenticada no ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

A maioria das teclas do anel de chave irá conter alguma forma de entropia e terá informações algorítmicos informando "criptografia de modo CBC + validação HMAC" ou "criptografia GCM + validação". Nesses casos, nos referimos a entropia incorporada como o material de chave mestre (ou KM) para essa chave, e podemos executar uma função de derivação de chave para derivar as chaves que serão usadas para operações de criptografia reais.

> [!NOTE]
> As chaves são abstratas e uma implementação personalizada pode não se comportar conforme mostrado a seguir. Se a chave fornece sua própria implementação do `IAuthenticatedEncryptor` em vez de usar uma das nossas fábricas internos, o mecanismo descrito nesta seção não se aplica.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Dados adicionais de autenticada e a derivação de subchaves

O `IAuthenticatedEncryptor` interface serve como a interface principal para todas as operações de criptografia autenticada. Seu `Encrypt` método utiliza dois buffers: texto sem formatação e additionalAuthenticatedData (AAD). O fluxo de conteúdo de texto sem formatação inalterado a chamada para `IDataProtector.Protect`, mas o AAD é gerado pelo sistema e consiste em três componentes:

1. 32 bits mágico cabeçalho 09 F0 C9 F0 que identifica esta versão do sistema de proteção de dados.

2. A id da chave de 128 bits.

3. Uma cadeia de caracteres de comprimento variável formadas a partir da cadeia de finalidade que criou o `IDataProtector` que está executando esta operação.

Como o AAD é exclusivo para a tupla de todos os três componentes, podemos usar isso para derivar novas chaves de KM em vez de usar KM em si em todos os de nossas operações criptográficas. Para todas as chamadas para `IAuthenticatedEncryptor.Encrypt`, ocorre o processo de derivação de chave a seguir:

( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)

Aqui, estamos ligando para o NIST SP800 108 KDF no modo de contador (consulte [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 5.1 seg.) com os seguintes parâmetros:

* Chave de derivação de chave (KDK) = K_M

* PRF = HMACSHA512

* label = additionalAuthenticatedData

* context = contextHeader || keyModifier

O cabeçalho de contexto é de comprimento variável e, essencialmente, serve como uma impressão digital dos algoritmos para o qual estamos estiver derivando K_E e K_H. O modificador de chave é uma cadeia de caracteres de 128 bits gerada aleatoriamente para cada chamada para `Encrypt` e serve para garantir a sobrecarregar a probabilidade de que KE e KH são exclusivos para essa operação de criptografia de autenticação específico, mesmo se todas as outras entradas para o KDF é constante.

Para criptografia de modo CBC + operações de validação do HMAC, | K_E | é o comprimento da chave de criptografia de bloco simétricas, e | K_H | é o tamanho do resumo da rotina HMAC. Para a criptografia do GCM + operações de validação, | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>Criptografia do modo CBC + validação HMAC

Quando K_E é gerado por meio do mecanismo acima, podemos gerar um vetor de inicialização aleatório e executar o algoritmo de criptografia de bloco simétricas para codificação de texto não criptografado. O vetor de inicialização e o texto cifrado, em seguida, executar a rotina de HMAC inicializada com a chave K_H para produzir o Mac. Esse processo e o valor de retorno é representada graficamente abaixo.

![Processo do modo CBC e retorno](subkeyderivation/_static/cbcprocess.png)

*output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))*

> [!NOTE]
> O `IDataProtector.Protect` implementação será [preceder o cabeçalho mágico e a id da chave](xref:security/data-protection/implementation/authenticated-encryption-details) a saída antes de retornar ao chamador. Porque o cabeçalho mágico e a id da chave são, implicitamente, faz parte do [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), e porque o modificador de chave é passado como entrada para o KDF, isso significa que cada byte da carga retornada final é autenticado pelo Mac.

## <a name="galoiscounter-mode-encryption--validation"></a>Criptografia de modo Galois/contador + validação

Quando K_E é gerado por meio do mecanismo acima, podemos gerar um nonce de 96 bits aleatório e executar o algoritmo de criptografia de bloco simétricas para codificação de texto não criptografado e gerar a marca de autenticação de 128 bits.

![Retorno e processo do modo de GCM](subkeyderivation/_static/galoisprocess.png)

*output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag*

> [!NOTE]
> Mesmo que o GCM nativamente suporta o conceito de AAD, podemos estiver ainda sendo alimentado AAD somente para o KDF original, optando por passar uma cadeia de caracteres vazia para o GCM para o parâmetro do AAD. A razão para isso é composto por duas etapas. Primeiro, [para dar suporte a agilidade](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) nunca queremos usar K_M diretamente como a chave de criptografia. Além disso, o GCM impõe requisitos de exclusividade muito estrito em suas entradas. A probabilidade de a rotina de criptografia do GCM é nunca invocado em dois ou mais diferentes conjuntos de dados de entrada com o mesmo (chave, nonce) par não deve exceder 2 ^ 32. Se podemos corrigir K_E não foi possível realizar mais de 2 ^ 32 operações de criptografia antes que podemos executar afoul sobre a 2 ^ limitar -32. Isso pode parecer um grande número de operações, mas um servidor web de alto tráfego pode passar por 4 bilhões de solicitações em meros dias, bem, dentro do tempo de vida normal para essas chaves. A manter a conformidade de 2 ^ limite de probabilidade-32, podemos continuar a usar um modificador de chave de 128 bits e o nonce de 96 bits, que estende radicalmente a contagem de operações utilizável para qualquer determinado K_M. Para manter a simplicidade do design, podemos compartilhar o caminho do código KDF entre as operações CBC e GCM, e uma vez que o AAD já é considerado no KDF não é necessário para encaminhá-lo para a rotina do GCM.
