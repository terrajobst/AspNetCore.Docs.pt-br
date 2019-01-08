---
title: Usar o streaming em SignalR do ASP.NET Core
author: tdykstra
description: ''
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: e0d201a7ffebbbe387a874c6d788994faa2be7a5
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098799"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="1e856-102">Usar o streaming em SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1e856-102">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="1e856-103">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="1e856-103">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="1e856-104">SignalR do ASP.NET Core dá suporte a streaming valores de retorno dos métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="1e856-104">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="1e856-105">Isso é útil para cenários em que os fragmentos de dados serão apresentadas ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="1e856-105">This is useful for scenarios where fragments of data will come in over time.</span></span> <span data-ttu-id="1e856-106">Quando um valor de retorno é transmitido ao cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="1e856-106">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

<span data-ttu-id="1e856-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e856-107">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-the-hub"></a><span data-ttu-id="1e856-108">Configurar o hub</span><span class="sxs-lookup"><span data-stu-id="1e856-108">Set up the hub</span></span>

<span data-ttu-id="1e856-109">Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um `ChannelReader<T>` ou um `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="1e856-109">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>` or a `Task<ChannelReader<T>>`.</span></span> <span data-ttu-id="1e856-110">Abaixo está um exemplo que mostra os conceitos básicos do fluxo de dados para o cliente.</span><span class="sxs-lookup"><span data-stu-id="1e856-110">Below is a sample that shows the basics of streaming data to the client.</span></span> <span data-ttu-id="1e856-111">Sempre que um objeto é gravado o `ChannelReader` esse objeto é enviado imediatamente para o cliente.</span><span class="sxs-lookup"><span data-stu-id="1e856-111">Whenever an object is written to the `ChannelReader` that object is immediately sent to the client.</span></span> <span data-ttu-id="1e856-112">No final, o `ChannelReader` estiver concluído para dizer ao cliente o fluxo está fechado.</span><span class="sxs-lookup"><span data-stu-id="1e856-112">At the end, the `ChannelReader` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> * <span data-ttu-id="1e856-113">Gravar o `ChannelReader` em um thread em segundo plano e retorne o `ChannelReader` assim que possível.</span><span class="sxs-lookup"><span data-stu-id="1e856-113">Write to the `ChannelReader` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="1e856-114">Outras chamadas de hub serão bloqueadas até que um `ChannelReader` é retornado.</span><span class="sxs-lookup"><span data-stu-id="1e856-114">Other hub invocations will be blocked until a `ChannelReader` is returned.</span></span>
> * <span data-ttu-id="1e856-115">Encapsular sua lógica em uma `try ... catch` e conclua o `Channel` em catch e fora de catch para garantir que o hub de invocação de método é concluída corretamente.</span><span class="sxs-lookup"><span data-stu-id="1e856-115">Wrap your logic in a `try ... catch` and complete the `Channel` in the catch and outside the catch to make sure the hub method invocation is completed properly.</span></span>

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

<span data-ttu-id="1e856-116">No ASP.NET Core 2.2 ou posterior, os métodos de Hub de streaming podem aceitar um `CancellationToken` parâmetro que será acionado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="1e856-116">In ASP.NET Core 2.2 or later, streaming Hub methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="1e856-117">Use esse token para interromper a operação do servidor e liberar quaisquer recursos se o cliente se desconecta antes do final do fluxo.</span><span class="sxs-lookup"><span data-stu-id="1e856-117">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="1e856-118">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1e856-118">.NET client</span></span>

<span data-ttu-id="1e856-119">O `StreamAsChannelAsync` método no `HubConnection` é usado para invocar um método de transmissão.</span><span class="sxs-lookup"><span data-stu-id="1e856-119">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a streaming method.</span></span> <span data-ttu-id="1e856-120">Passe o nome do método de hub e argumentos definidos no método de hub para `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="1e856-120">Pass the hub method name, and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="1e856-121">O parâmetro genérico em `StreamAsChannelAsync<T>` Especifica o tipo de objetos retornados pelo método de transmissão.</span><span class="sxs-lookup"><span data-stu-id="1e856-121">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="1e856-122">Um `ChannelReader<T>` é retornada da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="1e856-122">A `ChannelReader<T>` is returned from the stream invocation, and represents the stream on the client.</span></span> <span data-ttu-id="1e856-123">Para ler dados, um padrão comum é executar um loop sobre `WaitToReadAsync` e chamar `TryRead` quando os dados estiverem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="1e856-123">To read data, a common pattern is to loop over `WaitToReadAsync` and call `TryRead` when data is available.</span></span> <span data-ttu-id="1e856-124">O loop terminará quando o fluxo foi fechado pelo servidor ou o token de cancelamento passado para `StreamAsChannelAsync` é cancelada.</span><span class="sxs-lookup"><span data-stu-id="1e856-124">The loop will end when the stream has been closed by the server, or the cancellation token passed to `StreamAsChannelAsync` is canceled.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to 
// the server, which will trigger the corresponding token in the Hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="1e856-125">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1e856-125">JavaScript client</span></span>

<span data-ttu-id="1e856-126">Os clientes JavaScript chamar métodos de streaming em hubs usando `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="1e856-126">JavaScript clients call streaming methods on hubs by using `connection.stream`.</span></span> <span data-ttu-id="1e856-127">O `stream` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="1e856-127">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="1e856-128">O nome do método de hub.</span><span class="sxs-lookup"><span data-stu-id="1e856-128">The name of the hub method.</span></span> <span data-ttu-id="1e856-129">No exemplo a seguir, o nome do método de hub é `Counter`.</span><span class="sxs-lookup"><span data-stu-id="1e856-129">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="1e856-130">Argumentos definidos no método de hub.</span><span class="sxs-lookup"><span data-stu-id="1e856-130">Arguments defined in the hub method.</span></span> <span data-ttu-id="1e856-131">No exemplo a seguir, os argumentos são: uma contagem do número de itens de fluxo para receber e o atraso entre itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="1e856-131">In the following example, the arguments are: a count for the number of stream items to receive, and the delay between stream items.</span></span>

<span data-ttu-id="1e856-132">`connection.stream` Retorna um `IStreamResult` que contém um `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="1e856-132">`connection.stream` returns an `IStreamResult` which contains a `subscribe` method.</span></span> <span data-ttu-id="1e856-133">Passar uma `IStreamSubscriber` para `subscribe` e defina as `next`, `error`, e `complete` retornos de chamada para receber as notificações a `stream` invocação.</span><span class="sxs-lookup"><span data-stu-id="1e856-133">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to get notifications from the `stream` invocation.</span></span>

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="1e856-134">Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="1e856-134">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1e856-135">Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="1e856-135">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span> <span data-ttu-id="1e856-136">Chamar esse método fará com que o `CancellationToken` parâmetro do método de Hub (se você tiver fornecido um) a ser cancelada.</span><span class="sxs-lookup"><span data-stu-id="1e856-136">Calling this method will cause the `CancellationToken` parameter of the Hub method (if you provided one) to be canceled.</span></span>

::: moniker-end

## <a name="related-resources"></a><span data-ttu-id="1e856-137">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="1e856-137">Related resources</span></span>

* [<span data-ttu-id="1e856-138">Hubs</span><span class="sxs-lookup"><span data-stu-id="1e856-138">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1e856-139">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="1e856-139">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1e856-140">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="1e856-140">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="1e856-141">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="1e856-141">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
