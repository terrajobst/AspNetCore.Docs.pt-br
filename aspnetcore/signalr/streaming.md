---
title: Usar o streaming em SignalR do ASP.NET Core
author: bradygaster
description: Saiba como fluxos de valores de retorno de métodos de hub do servidor e consumir os fluxos usando os clientes .NET e JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: 7c176e3f21ffca7b97d9d3c2e8861032f22587b8
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264300"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="79c5c-103">Usar o streaming em SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79c5c-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="79c5c-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="79c5c-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="79c5c-105">SignalR do ASP.NET Core dá suporte a streaming valores de retorno dos métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="79c5c-105">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="79c5c-106">Isso é útil para cenários em que os fragmentos de dados serão apresentadas ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="79c5c-106">This is useful for scenarios where fragments of data will come in over time.</span></span> <span data-ttu-id="79c5c-107">Quando um valor de retorno é transmitido ao cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="79c5c-107">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

<span data-ttu-id="79c5c-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79c5c-108">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-the-hub"></a><span data-ttu-id="79c5c-109">Configurar o hub</span><span class="sxs-lookup"><span data-stu-id="79c5c-109">Set up the hub</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79c5c-110">Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, ou `Task<IAsyncEnumerable<T>>`.</span><span class="sxs-lookup"><span data-stu-id="79c5c-110">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="79c5c-111">Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um `ChannelReader<T>` ou um `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="79c5c-111">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>` or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79c5c-112">No ASP.NET Core 3.0 ou posterior, os métodos de hub de streaming podem retornar `IAsyncEnumerable<T>` além `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="79c5c-112">In ASP.NET Core 3.0 or later, streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="79c5c-113">A maneira mais simples de retornar `IAsyncEnumerable<T>` é, tornando o método de hub em um método de iterador assíncrono como o exemplo a seguir demonstra.</span><span class="sxs-lookup"><span data-stu-id="79c5c-113">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="79c5c-114">Métodos de iterador assíncrono hub podem aceitar um `CancellationToken` parâmetro que será acionado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="79c5c-114">Hub async iterator methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="79c5c-115">Métodos de iterador assíncrono evitar problemas comuns com canais com facilidade como não retornar os `ChannelReader` cedo suficiente ou saindo do método sem concluir o `ChannelWriter`.</span><span class="sxs-lookup"><span data-stu-id="79c5c-115">Async iterator methods easily avoid problems common with Channels such as not returning the `ChannelReader` early enough or exiting the method without completing the `ChannelWriter`.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/sample/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="79c5c-116">O exemplo a seguir mostra os conceitos básicos do fluxo de dados para o cliente usando canais.</span><span class="sxs-lookup"><span data-stu-id="79c5c-116">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="79c5c-117">Sempre que um objeto é gravado o `ChannelWriter` esse objeto é enviado imediatamente para o cliente.</span><span class="sxs-lookup"><span data-stu-id="79c5c-117">Whenever an object is written to the `ChannelWriter` that object is immediately sent to the client.</span></span> <span data-ttu-id="79c5c-118">No final, o `ChannelWriter` estiver concluído para dizer ao cliente o fluxo está fechado.</span><span class="sxs-lookup"><span data-stu-id="79c5c-118">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> * <span data-ttu-id="79c5c-119">Gravar o `ChannelWriter` em um thread em segundo plano e retorne o `ChannelReader` assim que possível.</span><span class="sxs-lookup"><span data-stu-id="79c5c-119">Write to the `ChannelWriter` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="79c5c-120">Outras chamadas de hub serão bloqueadas até que um `ChannelReader` é retornado.</span><span class="sxs-lookup"><span data-stu-id="79c5c-120">Other hub invocations will be blocked until a `ChannelReader` is returned.</span></span>
> * <span data-ttu-id="79c5c-121">Encapsular sua lógica em uma `try ... catch` e conclua o `Channel` em catch e fora de catch para garantir que o hub de invocação de método é concluída corretamente.</span><span class="sxs-lookup"><span data-stu-id="79c5c-121">Wrap your logic in a `try ... catch` and complete the `Channel` in the catch and outside the catch to make sure the hub method invocation is completed properly.</span></span>

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

<span data-ttu-id="79c5c-122">No ASP.NET Core 2.2 ou posterior, os métodos de hub de streaming podem aceitar um `CancellationToken` parâmetro que será acionado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="79c5c-122">In ASP.NET Core 2.2 or later, streaming hub methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="79c5c-123">Use esse token para interromper a operação do servidor e liberar quaisquer recursos se o cliente se desconecta antes do final do fluxo.</span><span class="sxs-lookup"><span data-stu-id="79c5c-123">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="79c5c-124">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="79c5c-124">.NET client</span></span>

<span data-ttu-id="79c5c-125">O `StreamAsChannelAsync` método no `HubConnection` é usado para invocar um método de transmissão.</span><span class="sxs-lookup"><span data-stu-id="79c5c-125">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a streaming method.</span></span> <span data-ttu-id="79c5c-126">Passe o nome do método de hub e argumentos definidos no método de hub para `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="79c5c-126">Pass the hub method name, and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="79c5c-127">O parâmetro genérico em `StreamAsChannelAsync<T>` Especifica o tipo de objetos retornados pelo método de transmissão.</span><span class="sxs-lookup"><span data-stu-id="79c5c-127">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="79c5c-128">Um `ChannelReader<T>` é retornada da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="79c5c-128">A `ChannelReader<T>` is returned from the stream invocation, and represents the stream on the client.</span></span> <span data-ttu-id="79c5c-129">Para ler dados, um padrão comum é executar um loop sobre `WaitToReadAsync` e chamar `TryRead` quando os dados estiverem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="79c5c-129">To read data, a common pattern is to loop over `WaitToReadAsync` and call `TryRead` when data is available.</span></span> <span data-ttu-id="79c5c-130">O loop terminará quando o fluxo foi fechado pelo servidor ou o token de cancelamento passado para `StreamAsChannelAsync` é cancelada.</span><span class="sxs-lookup"><span data-stu-id="79c5c-130">The loop will end when the stream has been closed by the server, or the cancellation token passed to `StreamAsChannelAsync` is canceled.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
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

## <a name="javascript-client"></a><span data-ttu-id="79c5c-131">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="79c5c-131">JavaScript client</span></span>

<span data-ttu-id="79c5c-132">Os clientes JavaScript chamar métodos de streaming em hubs usando `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="79c5c-132">JavaScript clients call streaming methods on hubs by using `connection.stream`.</span></span> <span data-ttu-id="79c5c-133">O `stream` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="79c5c-133">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="79c5c-134">O nome do método de hub.</span><span class="sxs-lookup"><span data-stu-id="79c5c-134">The name of the hub method.</span></span> <span data-ttu-id="79c5c-135">No exemplo a seguir, o nome do método de hub é `Counter`.</span><span class="sxs-lookup"><span data-stu-id="79c5c-135">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="79c5c-136">Argumentos definidos no método de hub.</span><span class="sxs-lookup"><span data-stu-id="79c5c-136">Arguments defined in the hub method.</span></span> <span data-ttu-id="79c5c-137">No exemplo a seguir, os argumentos são: uma contagem do número de itens de fluxo para receber e o atraso entre itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="79c5c-137">In the following example, the arguments are: a count for the number of stream items to receive, and the delay between stream items.</span></span>

<span data-ttu-id="79c5c-138">`connection.stream` Retorna um `IStreamResult` que contém um `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="79c5c-138">`connection.stream` returns an `IStreamResult` which contains a `subscribe` method.</span></span> <span data-ttu-id="79c5c-139">Passar uma `IStreamSubscriber` para `subscribe` e defina as `next`, `error`, e `complete` retornos de chamada para receber as notificações a `stream` invocação.</span><span class="sxs-lookup"><span data-stu-id="79c5c-139">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to get notifications from the `stream` invocation.</span></span>

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="79c5c-140">Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="79c5c-140">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="79c5c-141">Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="79c5c-141">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span> <span data-ttu-id="79c5c-142">Chamar esse método fará com que o `CancellationToken` parâmetro do método de Hub (se você tiver fornecido um) a ser cancelada.</span><span class="sxs-lookup"><span data-stu-id="79c5c-142">Calling this method will cause the `CancellationToken` parameter of the Hub method (if you provided one) to be canceled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="java-client"></a><span data-ttu-id="79c5c-143">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="79c5c-143">Java client</span></span>

<span data-ttu-id="79c5c-144">O cliente SignalR Java usa o `stream` método para invocar métodos de streaming.</span><span class="sxs-lookup"><span data-stu-id="79c5c-144">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="79c5c-145">Ela aceita três ou mais argumentos:</span><span class="sxs-lookup"><span data-stu-id="79c5c-145">It accepts three or more arguments:</span></span>

* <span data-ttu-id="79c5c-146">O tipo esperado de itens de fluxo</span><span class="sxs-lookup"><span data-stu-id="79c5c-146">The expected type of the stream items</span></span>
* <span data-ttu-id="79c5c-147">O nome do método de hub.</span><span class="sxs-lookup"><span data-stu-id="79c5c-147">The name of the hub method.</span></span>
* <span data-ttu-id="79c5c-148">Argumentos definidos no método de hub.</span><span class="sxs-lookup"><span data-stu-id="79c5c-148">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="79c5c-149">O `stream` método no `HubConnection` retorna um observável do tipo de item de fluxo.</span><span class="sxs-lookup"><span data-stu-id="79c5c-149">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="79c5c-150">O tipo observável `subscribe` método é onde você define sua `onNext`, `onError` e `onCompleted` manipuladores.</span><span class="sxs-lookup"><span data-stu-id="79c5c-150">The Observable type's `subscribe` method is where you define your `onNext`,  `onError` and  `onCompleted` handlers.</span></span>

::: moniker-end

## <a name="related-resources"></a><span data-ttu-id="79c5c-151">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="79c5c-151">Related resources</span></span>

* [<span data-ttu-id="79c5c-152">Hubs</span><span class="sxs-lookup"><span data-stu-id="79c5c-152">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="79c5c-153">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="79c5c-153">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="79c5c-154">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="79c5c-154">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="79c5c-155">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="79c5c-155">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
