---
title: Chamar serviços gRPCs com o cliente .NET
author: jamesnk
description: Saiba como chamar serviços gRPCs com o cliente .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 1e7887388a752fb35d00e65db210c3924c6ab192
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829095"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="ef3e5-103">Chamar serviços gRPCs com o cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ef3e5-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="ef3e5-104">Uma biblioteca de cliente .NET gRPC está disponível no pacote NuGet [gRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="ef3e5-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="ef3e5-105">Este documento explica como:</span><span class="sxs-lookup"><span data-stu-id="ef3e5-105">This document explains how to:</span></span>

* <span data-ttu-id="ef3e5-106">Configure um cliente gRPC para chamar os serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="ef3e5-107">Faça chamadas gRPC para métodos unários, transmissão de servidor, streaming de cliente e streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="ef3e5-108">Configurar o cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="ef3e5-108">Configure gRPC client</span></span>

<span data-ttu-id="ef3e5-109">Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de arquivos *\*. proto* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="ef3e5-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="ef3e5-110">O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no arquivo *\*. proto* .</span><span class="sxs-lookup"><span data-stu-id="ef3e5-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="ef3e5-111">Um cliente gRPC é criado a partir de um canal.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="ef3e5-112">Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="ef3e5-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="ef3e5-113">Um canal representa uma conexão de vida útil longa para um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="ef3e5-114">Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="ef3e5-115">Por exemplo, o `HttpClient` usado para fazer chamadas, o tamanho máximo de mensagens de envio e recebimento e o registro em log podem ser especificados em `GrpcChannelOptions` e usados com `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="ef3e5-116">Para obter uma lista completa de opções, consulte [Opções de configuração do cliente](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="ef3e5-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="ef3e5-117">Desempenho e uso do canal e do cliente:</span><span class="sxs-lookup"><span data-stu-id="ef3e5-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="ef3e5-118">A criação de um canal pode ser uma operação cara.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="ef3e5-119">Reutilizar um canal para chamadas gRPC fornece benefícios de desempenho.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="ef3e5-120">Os clientes do gRPC são criados com canais.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="ef3e5-121">Os clientes gRPC são objetos leves e não precisam ser armazenados em cache ou reutilizados.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="ef3e5-122">Vários clientes gRPC podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="ef3e5-123">Um canal e clientes criados a partir do canal podem ser usados com segurança por vários threads.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="ef3e5-124">Clientes criados a partir do canal podem fazer várias chamadas simultâneas.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="ef3e5-125">`GrpcChannel.ForAddress` não é a única opção para criar um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="ef3e5-126">Se você estiver chamando serviços gRPC de um aplicativo ASP.NET Core, considere a [integração do gRPC Client Factory](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="ef3e5-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="ef3e5-127">a integração do gRPC com o `HttpClientFactory` oferece uma alternativa centralizada para a criação de clientes do gRPC.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="ef3e5-128">A configuração adicional é necessária para [chamar serviços gRPCs inseguros com o cliente .net](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="ef3e5-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="ef3e5-129">Fazer chamadas gRPC</span><span class="sxs-lookup"><span data-stu-id="ef3e5-129">Make gRPC calls</span></span>

<span data-ttu-id="ef3e5-130">Uma chamada gRPC é iniciada chamando um método no cliente.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-130">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="ef3e5-131">O cliente gRPC tratará da serialização de mensagens e abordará a chamada gRPC para o serviço correto.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-131">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="ef3e5-132">gRPC tem tipos diferentes de métodos.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-132">gRPC has different types of methods.</span></span> <span data-ttu-id="ef3e5-133">A maneira como você usa o cliente para fazer uma chamada gRPC depende do tipo de método que você está chamando.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-133">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="ef3e5-134">Os tipos de método gRPC são:</span><span class="sxs-lookup"><span data-stu-id="ef3e5-134">The gRPC method types are:</span></span>

* <span data-ttu-id="ef3e5-135">Unário</span><span class="sxs-lookup"><span data-stu-id="ef3e5-135">Unary</span></span>
* <span data-ttu-id="ef3e5-136">Transmissão de servidor</span><span class="sxs-lookup"><span data-stu-id="ef3e5-136">Server streaming</span></span>
* <span data-ttu-id="ef3e5-137">Streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="ef3e5-137">Client streaming</span></span>
* <span data-ttu-id="ef3e5-138">Streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="ef3e5-138">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="ef3e5-139">Chamada unário</span><span class="sxs-lookup"><span data-stu-id="ef3e5-139">Unary call</span></span>

<span data-ttu-id="ef3e5-140">Uma chamada unário começa com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-140">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="ef3e5-141">Uma mensagem de resposta é retornada quando o serviço é concluído.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-141">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="ef3e5-142">Cada método de serviço unário no arquivo *\*. proto* resultará em dois métodos .net no tipo de cliente concreto gRPC para chamar o método: um método assíncrono e um método de bloqueio.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-142">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="ef3e5-143">Por exemplo, em `GreeterClient` há duas maneiras de chamar `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="ef3e5-143">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="ef3e5-144">`GreeterClient.SayHelloAsync`-chama o serviço `Greeter.SayHello` de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-144">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="ef3e5-145">Pode ser esperado.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-145">Can be awaited.</span></span>
* <span data-ttu-id="ef3e5-146">`GreeterClient.SayHello`-chama o serviço `Greeter.SayHello` e bloqueia até que seja concluído.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-146">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="ef3e5-147">Não use em código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-147">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="ef3e5-148">Chamada de streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="ef3e5-148">Server streaming call</span></span>

<span data-ttu-id="ef3e5-149">Uma chamada de transmissão de servidor inicia com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-149">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="ef3e5-150">`ResponseStream.MoveNext()` lê mensagens transmitidas do serviço.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-150">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="ef3e5-151">A chamada de streaming de servidor é concluída quando `ResponseStream.MoveNext()` retorna `false`.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-151">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

<span data-ttu-id="ef3e5-152">Se você estiver usando C# 8 ou posterior, a sintaxe de `await foreach` poderá ser usada para ler mensagens.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-152">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="ef3e5-153">O método de extensão `IAsyncStreamReader<T>.ReadAllAsync()` lê todas as mensagens do fluxo de resposta:</span><span class="sxs-lookup"><span data-stu-id="ef3e5-153">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="ef3e5-154">Chamada de streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="ef3e5-154">Client streaming call</span></span>

<span data-ttu-id="ef3e5-155">Uma chamada de streaming de cliente inicia *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-155">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="ef3e5-156">O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-156">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="ef3e5-157">Quando o cliente terminar de enviar mensagens `RequestStream.CompleteAsync` deverá ser chamado para notificar o serviço.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-157">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="ef3e5-158">A chamada é concluída quando o serviço retorna uma mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-158">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="ef3e5-159">Chamada de streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="ef3e5-159">Bi-directional streaming call</span></span>

<span data-ttu-id="ef3e5-160">Uma chamada de streaming bidirecional é iniciada *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-160">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="ef3e5-161">O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-161">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="ef3e5-162">As mensagens transmitidas do serviço são acessíveis com `ResponseStream.MoveNext()` ou `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-162">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="ef3e5-163">A chamada de streaming bidirecional é concluída quando o `ResponseStream` não tem mais mensagens.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-163">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

<span data-ttu-id="ef3e5-164">Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens entre si a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-164">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="ef3e5-165">A melhor lógica de cliente para interagir com uma chamada bidirecional varia dependendo da lógica do serviço.</span><span class="sxs-lookup"><span data-stu-id="ef3e5-165">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ef3e5-166">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ef3e5-166">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
