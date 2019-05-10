---
title: Considerações de design de API do SignalR
author: anurse
description: Aprenda a projetar APIs SignalR para compatibilidade entre versões do seu aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/api-design
ms.openlocfilehash: 3f17bf055b793e8fc91fbcc15f668928ca261f77
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897803"
---
# <a name="signalr-api-design-considerations"></a>Considerações de design de API do SignalR

Por [Andrew Stanton-Nurse](https://twitter.com/anurse)

Este artigo fornece diretrizes para a criação de APIs baseadas no SignalR.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Usar parâmetros de objeto personalizado para garantir a compatibilidade com versões anteriores

Adicionar parâmetros a um método de hub do SignalR (no cliente ou servidor) é um *alteração significativa*. Isso significa que os clientes/servidores mais antigos receberá erros ao tentar invocar o método sem o número apropriado de parâmetros. No entanto, é a adição de propriedades para um parâmetro de objeto personalizado **não** uma alteração significativa. Isso pode ser usado para projetar APIs compatíveis que são resistentes a alterações no cliente ou servidor.

Por exemplo, considere uma API de servidor semelhante ao seguinte:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

O cliente JavaScript chama esse método usando `invoke` da seguinte maneira:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Se você adicionar posteriormente um segundo parâmetro para o método de servidor, os clientes mais antigos não fornecerá esse valor de parâmetro. Por exemplo:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Quando o cliente antigo tenta invocar esse método, ele receberá um erro como este:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

No servidor, você verá uma mensagem de log como esta:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

O cliente antigo enviados apenas um parâmetro, mas a API mais recente do servidor necessários dois parâmetros. O uso de objetos personalizados como parâmetros oferece mais flexibilidade. Vamos recriar a API original para usar um objeto personalizado:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Agora, o cliente usa um objeto para chamar o método:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Em vez de adicionar um parâmetro, adicione uma propriedade para o `TotalLengthRequest` objeto:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Quando o cliente antigo envia um único parâmetro, o extra `Param2` propriedade será deixada `null`. Você pode detectar uma mensagem enviada por um cliente mais antigo, verificando o `Param2` para `null` e aplicar um valor padrão. Um novo cliente pode enviar os dois parâmetros.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

A mesma técnica funciona para métodos definidos no cliente. Você pode enviar um objeto personalizado do lado do servidor:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

No lado do cliente, você acessa o `Message` propriedade em vez de usar um parâmetro:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Se você decidir posteriormente adicionar o remetente da mensagem à carga, adicione uma propriedade no objeto:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Os clientes mais antigos não esperando o `Sender` de valor, portanto eles vai ignorá-lo. Um novo cliente pode aceitá-lo com a atualização para a nova propriedade de leitura:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

Nesse caso, o novo cliente também é tolerante a falhas de um servidor antigo que não fornece o `Sender` valor. Uma vez que o servidor antigo não fornecerá a `Sender` valor, o cliente verifica para ver se ele existe antes de acessá-lo.
