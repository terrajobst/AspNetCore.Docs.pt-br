---
title: Usar Streaming no ASP.NET Core SignalR
author: bradygaster
description: Saiba como transmitir dados entre o cliente e o servidor.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 21dd8180fe168f81ed68b01f02b81a6264d6e5a6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667723"
---
# <a name="use-streaming-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="8742c-103">Usar Streaming no ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="8742c-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="8742c-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="8742c-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8742c-105">ASP.NET Core SignalR dá suporte ao streaming do cliente para o servidor e do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="8742c-106">Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="8742c-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="8742c-107">Ao transmitir, cada fragmento é enviado ao cliente ou ao servidor assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="8742c-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8742c-108">ASP.NET Core SignalR dá suporte a valores de retorno de transmissão de métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="8742c-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="8742c-109">Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="8742c-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="8742c-110">Quando um valor de retorno é transmitido para o cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="8742c-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="8742c-111">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8742c-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="8742c-112">Configurar um hub para streaming</span><span class="sxs-lookup"><span data-stu-id="8742c-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8742c-113">Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`ou `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="8742c-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8742c-114">Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna um <xref:System.Threading.Channels.ChannelReader%601> ou um `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="8742c-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="8742c-115">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="8742c-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8742c-116">Os métodos de Hub de streaming podem retornar `IAsyncEnumerable<T>` além de `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="8742c-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="8742c-117">A maneira mais simples de retornar `IAsyncEnumerable<T>` é fazendo com que o método de Hub seja um método de iterador assíncrono como demonstrado pelo exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="8742c-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="8742c-118">Os métodos iteradores assíncronos de Hub podem aceitar um parâmetro `CancellationToken` que é disparado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="8742c-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="8742c-119">Os métodos iteradores assíncronos evitam problemas comuns com canais, como não retornar o `ChannelReader` cedo o suficiente ou sair do método sem concluir a <xref:System.Threading.Channels.ChannelWriter`1>.</span><span class="sxs-lookup"><span data-stu-id="8742c-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="8742c-120">O exemplo a seguir mostra as noções básicas de streaming de dados para o cliente usando canais.</span><span class="sxs-lookup"><span data-stu-id="8742c-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="8742c-121">Sempre que um objeto é gravado no <xref:System.Threading.Channels.ChannelWriter%601>, o objeto é enviado imediatamente para o cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="8742c-122">No final, a `ChannelWriter` é concluída para informar ao cliente que o fluxo está fechado.</span><span class="sxs-lookup"><span data-stu-id="8742c-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="8742c-123">Grave no `ChannelWriter<T>` em um thread em segundo plano e retorne o `ChannelReader` assim que possível.</span><span class="sxs-lookup"><span data-stu-id="8742c-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="8742c-124">Outras invocações de Hub são bloqueadas até que um `ChannelReader` seja retornado.</span><span class="sxs-lookup"><span data-stu-id="8742c-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="8742c-125">Ajustar a lógica em um `try ... catch`.</span><span class="sxs-lookup"><span data-stu-id="8742c-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="8742c-126">Conclua a `Channel` no `catch` e fora do `catch` para verificar se a invocação do método de Hub foi concluída corretamente.</span><span class="sxs-lookup"><span data-stu-id="8742c-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="8742c-127">Os métodos de Hub de streaming de servidor para cliente podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo.</span><span class="sxs-lookup"><span data-stu-id="8742c-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="8742c-128">Use esse token para interromper a operação do servidor e liberar todos os recursos se o cliente se desconectar antes do final do fluxo.</span><span class="sxs-lookup"><span data-stu-id="8742c-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="8742c-129">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="8742c-129">Client-to-server streaming</span></span>

<span data-ttu-id="8742c-130">Um método de Hub se torna automaticamente um método de Hub de streaming de cliente para servidor quando ele aceita um ou mais objetos do tipo <xref:System.Threading.Channels.ChannelReader%601> ou <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span><span class="sxs-lookup"><span data-stu-id="8742c-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="8742c-131">O exemplo a seguir mostra as noções básicas de leitura de dados de streaming enviados do cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="8742c-132">Sempre que o cliente grava no <xref:System.Threading.Channels.ChannelWriter%601>, os dados são gravados no `ChannelReader` no servidor do qual o método de Hub está lendo.</span><span class="sxs-lookup"><span data-stu-id="8742c-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="8742c-133">Segue um <xref:System.Collections.Generic.IAsyncEnumerable%601> versão do método.</span><span class="sxs-lookup"><span data-stu-id="8742c-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="8742c-134">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="8742c-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="8742c-135">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="8742c-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8742c-136">Os métodos `StreamAsync` e `StreamAsChannelAsync` no `HubConnection` são usados para invocar métodos de transmissão de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="8742c-137">Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsync` ou `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="8742c-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="8742c-138">O parâmetro Generic em `StreamAsync<T>` e `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming.</span><span class="sxs-lookup"><span data-stu-id="8742c-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="8742c-139">Um objeto do tipo `IAsyncEnumerable<T>` ou `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="8742c-140">Um exemplo `StreamAsync` que retorna `IAsyncEnumerable<int>`:</span><span class="sxs-lookup"><span data-stu-id="8742c-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="8742c-141">Um exemplo de `StreamAsChannelAsync` correspondente que retorna `ChannelReader<int>`:</span><span class="sxs-lookup"><span data-stu-id="8742c-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8742c-142">O método `StreamAsChannelAsync` em `HubConnection` é usado para invocar um método de transmissão de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="8742c-143">Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="8742c-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="8742c-144">O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming.</span><span class="sxs-lookup"><span data-stu-id="8742c-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="8742c-145">Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="8742c-146">O método `StreamAsChannelAsync` em `HubConnection` é usado para invocar um método de transmissão de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="8742c-147">Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="8742c-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="8742c-148">O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming.</span><span class="sxs-lookup"><span data-stu-id="8742c-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="8742c-149">Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="8742c-150">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="8742c-150">Client-to-server streaming</span></span>

<span data-ttu-id="8742c-151">Há duas maneiras de invocar um método de Hub de transmissão de cliente para servidor do cliente .NET.</span><span class="sxs-lookup"><span data-stu-id="8742c-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="8742c-152">Você pode passar um `IAsyncEnumerable<T>` ou um `ChannelReader` como um argumento para `SendAsync`, `InvokeAsync`ou `StreamAsChannelAsync`, dependendo do método de Hub invocado.</span><span class="sxs-lookup"><span data-stu-id="8742c-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="8742c-153">Sempre que os dados são gravados no objeto `IAsyncEnumerable` ou `ChannelWriter`, o método Hub no servidor recebe um novo item com os dados do cliente.</span><span class="sxs-lookup"><span data-stu-id="8742c-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="8742c-154">Se estiver usando um objeto `IAsyncEnumerable`, o fluxo terminará depois que o método que retorna os itens de fluxo for encerrado.</span><span class="sxs-lookup"><span data-stu-id="8742c-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="8742c-155">Ou, se você estiver usando uma `ChannelWriter`, você concluirá o canal com `channel.Writer.Complete()`:</span><span class="sxs-lookup"><span data-stu-id="8742c-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="8742c-156">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="8742c-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="8742c-157">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="8742c-157">Server-to-client streaming</span></span>

<span data-ttu-id="8742c-158">Os clientes JavaScript chamam métodos de transmissão de servidor para cliente em hubs com `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="8742c-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="8742c-159">O método `stream` aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="8742c-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="8742c-160">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="8742c-160">The name of the hub method.</span></span> <span data-ttu-id="8742c-161">No exemplo a seguir, o nome do método de Hub é `Counter`.</span><span class="sxs-lookup"><span data-stu-id="8742c-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="8742c-162">Argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="8742c-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="8742c-163">No exemplo a seguir, os argumentos são uma contagem para o número de itens de fluxo a serem recebidos e o atraso entre itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="8742c-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="8742c-164">`connection.stream` retorna um `IStreamResult`, que contém um método `subscribe`.</span><span class="sxs-lookup"><span data-stu-id="8742c-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="8742c-165">Passe um `IStreamSubscriber` para `subscribe` e defina os retornos de chamada `next`, `error`e `complete` para receber notificações da invocação de `stream`.</span><span class="sxs-lookup"><span data-stu-id="8742c-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="8742c-166">Para finalizar o fluxo do cliente, chame o método `dispose` no `ISubscription` retornado do método `subscribe`.</span><span class="sxs-lookup"><span data-stu-id="8742c-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="8742c-167">Chamar esse método causa o cancelamento do parâmetro `CancellationToken` do método Hub, se você tiver fornecido um.</span><span class="sxs-lookup"><span data-stu-id="8742c-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="8742c-168">Para finalizar o fluxo do cliente, chame o método `dispose` no `ISubscription` retornado do método `subscribe`.</span><span class="sxs-lookup"><span data-stu-id="8742c-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="8742c-169">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="8742c-169">Client-to-server streaming</span></span>

<span data-ttu-id="8742c-170">Os clientes JavaScript chamam métodos de transmissão de cliente para servidor em hubs, passando um `Subject` como um argumento para `send`, `invoke`ou `stream`, dependendo do método de Hub invocado.</span><span class="sxs-lookup"><span data-stu-id="8742c-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="8742c-171">A `Subject` é uma classe parecida com uma `Subject`.</span><span class="sxs-lookup"><span data-stu-id="8742c-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="8742c-172">Por exemplo, em RxJS, você pode usar a classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) dessa biblioteca.</span><span class="sxs-lookup"><span data-stu-id="8742c-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="8742c-173">Chamar `subject.next(item)` com um item grava o item no fluxo e o método Hub recebe o item no servidor.</span><span class="sxs-lookup"><span data-stu-id="8742c-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="8742c-174">Para encerrar o fluxo, chame `subject.complete()`.</span><span class="sxs-lookup"><span data-stu-id="8742c-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="8742c-175">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="8742c-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="8742c-176">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="8742c-176">Server-to-client streaming</span></span>

<span data-ttu-id="8742c-177">O cliente Java SignalR usa o método `stream` para invocar métodos de streaming.</span><span class="sxs-lookup"><span data-stu-id="8742c-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="8742c-178">`stream` aceita três ou mais argumentos:</span><span class="sxs-lookup"><span data-stu-id="8742c-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="8742c-179">O tipo esperado dos itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="8742c-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="8742c-180">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="8742c-180">The name of the hub method.</span></span>
* <span data-ttu-id="8742c-181">Argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="8742c-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="8742c-182">O método `stream` em `HubConnection` retorna um observável do tipo de item de fluxo.</span><span class="sxs-lookup"><span data-stu-id="8742c-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="8742c-183">O método `subscribe` do tipo observável é onde `onNext`, `onError` e manipuladores de `onCompleted` são definidos.</span><span class="sxs-lookup"><span data-stu-id="8742c-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8742c-184">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8742c-184">Additional resources</span></span>

* [<span data-ttu-id="8742c-185">Hubs</span><span class="sxs-lookup"><span data-stu-id="8742c-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="8742c-186">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="8742c-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="8742c-187">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="8742c-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="8742c-188">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="8742c-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
