---
title: Usar o streaming em SignalR do ASP.NET Core
author: bradygaster
description: Saiba como transmitir dados entre o cliente e o servidor.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2019
uid: signalr/streaming
ms.openlocfilehash: 8f39fdfa45766b5bbec572970f009abefefdc419
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897193"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="77e1f-103">Usar o streaming em SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77e1f-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="77e1f-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="77e1f-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77e1f-105">SignalR do ASP.NET Core dá suporte a streaming do cliente ao servidor e do servidor ao cliente.</span><span class="sxs-lookup"><span data-stu-id="77e1f-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="77e1f-106">Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="77e1f-107">Quando o streaming, cada fragmento é enviado ao cliente ou servidor assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="77e1f-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77e1f-108">SignalR do ASP.NET Core dá suporte a streaming valores de retorno dos métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="77e1f-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="77e1f-109">Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="77e1f-110">Quando um valor de retorno é transmitido ao cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="77e1f-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="77e1f-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77e1f-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="77e1f-112">Configurar um hub para streaming</span><span class="sxs-lookup"><span data-stu-id="77e1f-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77e1f-113">Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um <xref:System.Threading.Channels.ChannelReader%601>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, ou `Task<IAsyncEnumerable<T>>`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-113">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77e1f-114">Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um <xref:System.Threading.Channels.ChannelReader%601> ou um `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="77e1f-115">Streaming Server-para-cliente</span><span class="sxs-lookup"><span data-stu-id="77e1f-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77e1f-116">Métodos de hub de streaming podem retornar `IAsyncEnumerable<T>` além `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="77e1f-117">A maneira mais simples de retornar `IAsyncEnumerable<T>` é, tornando o método de hub em um método de iterador assíncrono como o exemplo a seguir demonstra.</span><span class="sxs-lookup"><span data-stu-id="77e1f-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="77e1f-118">Métodos de iterador assíncrono hub podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="77e1f-119">Métodos de iterador assíncrono evitar problemas comuns com canais, como não retornar os `ChannelReader` cedo suficiente ou saindo do método sem concluir o <xref:System.Threading.Channels.ChannelWriter`1>.</span><span class="sxs-lookup"><span data-stu-id="77e1f-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="77e1f-120">O exemplo a seguir mostra os conceitos básicos do fluxo de dados para o cliente usando canais.</span><span class="sxs-lookup"><span data-stu-id="77e1f-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="77e1f-121">Sempre que um objeto é gravado para o <xref:System.Threading.Channels.ChannelWriter%601>, o objeto imediatamente é enviado ao cliente.</span><span class="sxs-lookup"><span data-stu-id="77e1f-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="77e1f-122">No final, o `ChannelWriter` estiver concluído para dizer ao cliente o fluxo está fechado.</span><span class="sxs-lookup"><span data-stu-id="77e1f-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="77e1f-123">Gravar o `ChannelWriter<T>` em um thread em segundo plano e retorne o `ChannelReader` assim que possível.</span><span class="sxs-lookup"><span data-stu-id="77e1f-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="77e1f-124">Outras chamadas de hub são bloqueadas até que um `ChannelReader` é retornado.</span><span class="sxs-lookup"><span data-stu-id="77e1f-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="77e1f-125">Encapsular a lógica em um `try ... catch`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="77e1f-126">Conclua o `Channel` no `catch` quanto fora o `catch` para garantir que o hub de invocação de método é concluída corretamente.</span><span class="sxs-lookup"><span data-stu-id="77e1f-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="77e1f-127">Métodos de hub do streaming Server para o cliente podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="77e1f-128">Use esse token para interromper a operação do servidor e liberar quaisquer recursos se o cliente se desconecta antes do final do fluxo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="77e1f-129">Cliente-servidor de streaming</span><span class="sxs-lookup"><span data-stu-id="77e1f-129">Client-to-server streaming</span></span>

<span data-ttu-id="77e1f-130">Um método de hub automaticamente se torna um método de hub de streaming de cliente-servidor quando ele aceita um ou mais <xref:System.Threading.Channels.ChannelReader`1>s.</span><span class="sxs-lookup"><span data-stu-id="77e1f-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more <xref:System.Threading.Channels.ChannelReader`1>s.</span></span> <span data-ttu-id="77e1f-131">O exemplo a seguir mostra as Noções básicas de leitura de dados de streaming enviados do cliente.</span><span class="sxs-lookup"><span data-stu-id="77e1f-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="77e1f-132">Sempre que o cliente grava os <xref:System.Threading.Channels.ChannelWriter`1>, os dados são gravados no `ChannelReader` no servidor que o método de hub esteja lendo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter`1>, the data is written into the `ChannelReader` on the server that the hub method is reading from.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="77e1f-133">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="77e1f-133">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="77e1f-134">Streaming Server-para-cliente</span><span class="sxs-lookup"><span data-stu-id="77e1f-134">Server-to-client streaming</span></span>

<span data-ttu-id="77e1f-135">O `StreamAsChannelAsync` método no `HubConnection` é usado para invocar um método de transmissão de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="77e1f-135">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="77e1f-136">Passe o nome do método de hub e argumentos definidos no método de hub para `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-136">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="77e1f-137">O parâmetro genérico em `StreamAsChannelAsync<T>` Especifica o tipo de objetos retornados pelo método de transmissão.</span><span class="sxs-lookup"><span data-stu-id="77e1f-137">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="77e1f-138">Um `ChannelReader<T>` é retornada da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="77e1f-138">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="77e1f-139">Cliente-servidor de streaming</span><span class="sxs-lookup"><span data-stu-id="77e1f-139">Client-to-server streaming</span></span>

<span data-ttu-id="77e1f-140">Para invocar um método de hub de streaming de cliente-servidor do cliente .NET, crie uma `Channel` e passe a `ChannelReader` como um argumento para `SendAsync`, `InvokeAsync`, ou `StreamAsChannelAsync`, dependendo do método de hub invocado.</span><span class="sxs-lookup"><span data-stu-id="77e1f-140">To invoke a client-to-server streaming hub method from the .NET client, create a `Channel` and pass the `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="77e1f-141">Sempre que os dados são gravados para o `ChannelWriter`, o método de hub no servidor recebe um novo item com os dados do cliente.</span><span class="sxs-lookup"><span data-stu-id="77e1f-141">Whenever data is written to the `ChannelWriter`, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="77e1f-142">Para encerrar o fluxo, conclua o canal com `channel.Writer.Complete()`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-142">To end the stream, complete the channel with `channel.Writer.Complete()`.</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="77e1f-143">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="77e1f-143">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="77e1f-144">Streaming Server-para-cliente</span><span class="sxs-lookup"><span data-stu-id="77e1f-144">Server-to-client streaming</span></span>

<span data-ttu-id="77e1f-145">Os clientes JavaScript chamem métodos de streaming do servidor-para-cliente em hubs com `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-145">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="77e1f-146">O `stream` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="77e1f-146">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="77e1f-147">O nome do método de hub.</span><span class="sxs-lookup"><span data-stu-id="77e1f-147">The name of the hub method.</span></span> <span data-ttu-id="77e1f-148">No exemplo a seguir, o nome do método de hub é `Counter`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-148">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="77e1f-149">Argumentos definidos no método de hub.</span><span class="sxs-lookup"><span data-stu-id="77e1f-149">Arguments defined in the hub method.</span></span> <span data-ttu-id="77e1f-150">No exemplo a seguir, os argumentos são uma contagem do número de itens de fluxo para receber e o atraso entre itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-150">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="77e1f-151">`connection.stream` Retorna um `IStreamResult`, que contém um `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="77e1f-151">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="77e1f-152">Passar uma `IStreamSubscriber` para `subscribe` e defina as `next`, `error`, e `complete` retornos de chamada para receber notificações do `stream` invocação.</span><span class="sxs-lookup"><span data-stu-id="77e1f-152">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="77e1f-153">Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="77e1f-153">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="77e1f-154">Chamar esse método faz com que o cancelamento do `CancellationToken` parâmetro do método de Hub, se você tiver fornecido um.</span><span class="sxs-lookup"><span data-stu-id="77e1f-154">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="77e1f-155">Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.</span><span class="sxs-lookup"><span data-stu-id="77e1f-155">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="77e1f-156">Cliente-servidor de streaming</span><span class="sxs-lookup"><span data-stu-id="77e1f-156">Client-to-server streaming</span></span>

<span data-ttu-id="77e1f-157">Os clientes JavaScript chamar métodos de streaming de cliente-servidor em hubs, passando um `Subject` como um argumento para `send`, `invoke`, ou `stream`, dependendo do método de hub invocado.</span><span class="sxs-lookup"><span data-stu-id="77e1f-157">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="77e1f-158">O `Subject` é uma classe que se parece com um `Subject`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-158">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="77e1f-159">Por exemplo no RxJS, você pode usar o [assunto](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) classe na biblioteca.</span><span class="sxs-lookup"><span data-stu-id="77e1f-159">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="77e1f-160">Chamar `subject.next(item)` com um item grava o item para o fluxo e o método de hub recebe o item no servidor.</span><span class="sxs-lookup"><span data-stu-id="77e1f-160">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="77e1f-161">Para terminar o fluxo, chame `subject.complete()`.</span><span class="sxs-lookup"><span data-stu-id="77e1f-161">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="77e1f-162">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="77e1f-162">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="77e1f-163">Streaming Server-para-cliente</span><span class="sxs-lookup"><span data-stu-id="77e1f-163">Server-to-client streaming</span></span>

<span data-ttu-id="77e1f-164">O cliente SignalR Java usa o `stream` método para invocar métodos de streaming.</span><span class="sxs-lookup"><span data-stu-id="77e1f-164">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="77e1f-165">`stream` aceita três ou mais argumentos:</span><span class="sxs-lookup"><span data-stu-id="77e1f-165">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="77e1f-166">O tipo esperado de itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-166">The expected type of the stream items.</span></span>
* <span data-ttu-id="77e1f-167">O nome do método de hub.</span><span class="sxs-lookup"><span data-stu-id="77e1f-167">The name of the hub method.</span></span>
* <span data-ttu-id="77e1f-168">Argumentos definidos no método de hub.</span><span class="sxs-lookup"><span data-stu-id="77e1f-168">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="77e1f-169">O `stream` método no `HubConnection` retorna um observável do tipo de item de fluxo.</span><span class="sxs-lookup"><span data-stu-id="77e1f-169">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="77e1f-170">O tipo observável `subscribe` método é onde `onNext`, `onError` e `onCompleted` manipuladores são definidos.</span><span class="sxs-lookup"><span data-stu-id="77e1f-170">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="77e1f-171">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="77e1f-171">Additional resources</span></span>

* [<span data-ttu-id="77e1f-172">Hubs</span><span class="sxs-lookup"><span data-stu-id="77e1f-172">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="77e1f-173">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="77e1f-173">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="77e1f-174">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="77e1f-174">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="77e1f-175">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="77e1f-175">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
