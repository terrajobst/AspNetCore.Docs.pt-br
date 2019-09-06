---
title: Chamar serviços gRPCs com o cliente .NET
author: jamesnk
description: Saiba como chamar serviços gRPCs com o cliente .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 5518a221c4641ba0d1da051a14750e3165944455
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310670"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="c1d49-103">Chamar serviços gRPCs com o cliente .NET</span><span class="sxs-lookup"><span data-stu-id="c1d49-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="c1d49-104">Uma biblioteca de cliente .NET gRPC está disponível no pacote NuGet [gRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="c1d49-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="c1d49-105">Este documento explica como:</span><span class="sxs-lookup"><span data-stu-id="c1d49-105">This document explains how to:</span></span>

* <span data-ttu-id="c1d49-106">Configure um cliente gRPC para chamar os serviços gRPC.</span><span class="sxs-lookup"><span data-stu-id="c1d49-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="c1d49-107">Faça chamadas gRPC para métodos unários, transmissão de servidor, streaming de cliente e streaming bidirecional.</span><span class="sxs-lookup"><span data-stu-id="c1d49-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="c1d49-108">Configurar o cliente gRPC</span><span class="sxs-lookup"><span data-stu-id="c1d49-108">Configure gRPC client</span></span>

<span data-ttu-id="c1d49-109">Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de  *\*arquivos. proto* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="c1d49-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="c1d49-110">O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no  *\*arquivo. proto* .</span><span class="sxs-lookup"><span data-stu-id="c1d49-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="c1d49-111">Um cliente gRPC é criado a partir de um canal.</span><span class="sxs-lookup"><span data-stu-id="c1d49-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="c1d49-112">Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="c1d49-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="c1d49-113">Um canal representa uma conexão de vida útil longa para um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="c1d49-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="c1d49-114">Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço.</span><span class="sxs-lookup"><span data-stu-id="c1d49-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="c1d49-115">Por exemplo, o `HttpClient` usado para fazer chamadas, o tamanho máximo de mensagens de envio e recebimento e o registro em `GrpcChannelOptions` log podem ser especificados `GrpcChannel.ForAddress`e usados com o.</span><span class="sxs-lookup"><span data-stu-id="c1d49-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="c1d49-116">Para obter uma lista completa de opções, consulte [Opções de configuração do cliente](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="c1d49-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="c1d49-117">Criar um canal pode ser uma operação cara e reutilizar um canal para chamadas gRPC oferece benefícios de desempenho.</span><span class="sxs-lookup"><span data-stu-id="c1d49-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="c1d49-118">Vários clientes gRPC concretos podem ser criados a partir de um canal, incluindo diferentes tipos de clientes.</span><span class="sxs-lookup"><span data-stu-id="c1d49-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="c1d49-119">Os tipos de cliente concretos gRPC são objetos leves e podem ser criados quando necessário.</span><span class="sxs-lookup"><span data-stu-id="c1d49-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="c1d49-120">`GrpcChannel.ForAddress`Não é a única opção para criar um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="c1d49-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="c1d49-121">Se você estiver chamando serviços gRPC de um aplicativo ASP.NET Core, considere a [integração do gRPC Client Factory](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="c1d49-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="c1d49-122">a integração do `HttpClientFactory` gRPC com oferece uma alternativa centralizada para a criação de clientes do gRPC.</span><span class="sxs-lookup"><span data-stu-id="c1d49-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="c1d49-123">Fazer chamadas gRPC</span><span class="sxs-lookup"><span data-stu-id="c1d49-123">Make gRPC calls</span></span>

<span data-ttu-id="c1d49-124">Uma chamada gRPC é iniciada chamando um método no cliente.</span><span class="sxs-lookup"><span data-stu-id="c1d49-124">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="c1d49-125">O cliente gRPC tratará da serialização de mensagens e abordará a chamada gRPC para o serviço correto.</span><span class="sxs-lookup"><span data-stu-id="c1d49-125">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="c1d49-126">gRPC tem tipos diferentes de métodos.</span><span class="sxs-lookup"><span data-stu-id="c1d49-126">gRPC has different types of methods.</span></span> <span data-ttu-id="c1d49-127">A maneira como você usa o cliente para fazer uma chamada gRPC depende do tipo de método que você está chamando.</span><span class="sxs-lookup"><span data-stu-id="c1d49-127">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="c1d49-128">Os tipos de método gRPC são:</span><span class="sxs-lookup"><span data-stu-id="c1d49-128">The gRPC method types are:</span></span>

* <span data-ttu-id="c1d49-129">Unário</span><span class="sxs-lookup"><span data-stu-id="c1d49-129">Unary</span></span>
* <span data-ttu-id="c1d49-130">Transmissão de servidor</span><span class="sxs-lookup"><span data-stu-id="c1d49-130">Server streaming</span></span>
* <span data-ttu-id="c1d49-131">Streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="c1d49-131">Client streaming</span></span>
* <span data-ttu-id="c1d49-132">Streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="c1d49-132">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="c1d49-133">Chamada unário</span><span class="sxs-lookup"><span data-stu-id="c1d49-133">Unary call</span></span>

<span data-ttu-id="c1d49-134">Uma chamada unário começa com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1d49-134">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="c1d49-135">Uma mensagem de resposta é retornada quando o serviço é concluído.</span><span class="sxs-lookup"><span data-stu-id="c1d49-135">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="c1d49-136">Cada método de serviço unário no  *\*arquivo. proto* resultará em dois métodos .net no tipo de cliente concreto gRPC para chamar o método: um método assíncrono e um método de bloqueio.</span><span class="sxs-lookup"><span data-stu-id="c1d49-136">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="c1d49-137">Por exemplo, `GreeterClient` há duas maneiras de chamar `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="c1d49-137">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="c1d49-138">`GreeterClient.SayHelloAsync`-chama `Greeter.SayHello` o serviço de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="c1d49-138">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="c1d49-139">Pode ser esperado.</span><span class="sxs-lookup"><span data-stu-id="c1d49-139">Can be awaited.</span></span>
* <span data-ttu-id="c1d49-140">`GreeterClient.SayHello`-chama `Greeter.SayHello` serviço e bloqueia até a conclusão.</span><span class="sxs-lookup"><span data-stu-id="c1d49-140">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="c1d49-141">Não use em código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="c1d49-141">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="c1d49-142">Chamada de streaming de servidor</span><span class="sxs-lookup"><span data-stu-id="c1d49-142">Server streaming call</span></span>

<span data-ttu-id="c1d49-143">Uma chamada de transmissão de servidor inicia com o cliente enviando uma mensagem de solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1d49-143">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="c1d49-144">`ResponseStream.MoveNext()`lê mensagens transmitidas do serviço.</span><span class="sxs-lookup"><span data-stu-id="c1d49-144">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="c1d49-145">A chamada de streaming de servidor é `ResponseStream.MoveNext()` concluída `false`quando retorna.</span><span class="sxs-lookup"><span data-stu-id="c1d49-145">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // Greeting: Hello World" is streamed multiple times
    }
}
```

<span data-ttu-id="c1d49-146">Se você estiver usando C# 8 ou posterior, a `await foreach` sintaxe poderá ser usada para ler mensagens.</span><span class="sxs-lookup"><span data-stu-id="c1d49-146">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="c1d49-147">O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de resposta:</span><span class="sxs-lookup"><span data-stu-id="c1d49-147">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is streamed multiple times
    }
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="c1d49-148">Chamada de streaming de cliente</span><span class="sxs-lookup"><span data-stu-id="c1d49-148">Client streaming call</span></span>

<span data-ttu-id="c1d49-149">Uma chamada de streaming de cliente inicia *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="c1d49-149">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="c1d49-150">O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="c1d49-150">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="c1d49-151">Quando o cliente terminar, o envio `RequestStream.CompleteAsync` de mensagens deve ser chamado para notificar o serviço.</span><span class="sxs-lookup"><span data-stu-id="c1d49-151">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="c1d49-152">A chamada é concluída quando o serviço retorna uma mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="c1d49-152">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="c1d49-153">Chamada de streaming bidirecional</span><span class="sxs-lookup"><span data-stu-id="c1d49-153">Bi-directional streaming call</span></span>

<span data-ttu-id="c1d49-154">Uma chamada de streaming bidirecional é iniciada *sem* que o cliente envie uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="c1d49-154">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="c1d49-155">O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="c1d49-155">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="c1d49-156">As mensagens transmitidas do serviço são acessíveis com `ResponseStream.MoveNext()` o `ResponseStream.ReadAllAsync()`ou o.</span><span class="sxs-lookup"><span data-stu-id="c1d49-156">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="c1d49-157">A chamada de streaming bidirecional é concluída quando o `ResponseStream` não tem mais mensagens.</span><span class="sxs-lookup"><span data-stu-id="c1d49-157">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="c1d49-158">Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens entre si a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="c1d49-158">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="c1d49-159">A melhor lógica de cliente para interagir com uma chamada bidirecional varia dependendo da lógica do serviço.</span><span class="sxs-lookup"><span data-stu-id="c1d49-159">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1d49-160">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c1d49-160">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
