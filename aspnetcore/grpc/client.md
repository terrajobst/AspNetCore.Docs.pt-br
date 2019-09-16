---
title: Chamar serviços gRPCs com o cliente .NET
author: jamesnk
description: Saiba como chamar serviços gRPCs com o cliente .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 56f79b303a8d53699e8eb6156d328c0da1259416
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011143"
---
# <a name="call-grpc-services-with-the-net-client"></a>Chamar serviços gRPCs com o cliente .NET

Uma biblioteca de cliente .NET gRPC está disponível no pacote NuGet [gRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) . Este documento explica como:

* Configure um cliente gRPC para chamar os serviços gRPC.
* Faça chamadas gRPC para métodos unários, transmissão de servidor, streaming de cliente e streaming bidirecional.

## <a name="configure-grpc-client"></a>Configurar o cliente gRPC

Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de  *\*arquivos. proto* ](xref:grpc/basics#generated-c-assets). O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no  *\*arquivo. proto* .

Um cliente gRPC é criado a partir de um canal. Comece usando `GrpcChannel.ForAddress` para criar um canal e, em seguida, use o canal para criar um cliente gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Um canal representa uma conexão de vida útil longa para um serviço gRPC. Quando um canal é criado, ele é configurado com opções relacionadas à chamada de um serviço. Por exemplo, o `HttpClient` usado para fazer chamadas, o tamanho máximo de mensagens de envio e recebimento e o registro em `GrpcChannelOptions` log podem ser especificados `GrpcChannel.ForAddress`e usados com o. Para obter uma lista completa de opções, consulte [Opções de configuração do cliente](xref:grpc/configuration#configure-client-options).

Criar um canal pode ser uma operação cara e reutilizar um canal para chamadas gRPC oferece benefícios de desempenho. Vários clientes gRPC concretos podem ser criados a partir de um canal, incluindo diferentes tipos de clientes. Os tipos de cliente concretos gRPC são objetos leves e podem ser criados quando necessário.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

`GrpcChannel.ForAddress`Não é a única opção para criar um cliente gRPC. Se você estiver chamando serviços gRPC de um aplicativo ASP.NET Core, considere a [integração do gRPC Client Factory](xref:grpc/clientfactory). a integração do `HttpClientFactory` gRPC com oferece uma alternativa centralizada para a criação de clientes do gRPC.

> [!NOTE]
> A configuração adicional é necessária para [chamar serviços gRPCs inseguros com o cliente .net](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

## <a name="make-grpc-calls"></a>Fazer chamadas gRPC

Uma chamada gRPC é iniciada chamando um método no cliente. O cliente gRPC tratará da serialização de mensagens e abordará a chamada gRPC para o serviço correto.

gRPC tem tipos diferentes de métodos. A maneira como você usa o cliente para fazer uma chamada gRPC depende do tipo de método que você está chamando. Os tipos de método gRPC são:

* Unário
* Transmissão de servidor
* Streaming de cliente
* Streaming bidirecional

### <a name="unary-call"></a>Chamada unário

Uma chamada unário começa com o cliente enviando uma mensagem de solicitação. Uma mensagem de resposta é retornada quando o serviço é concluído.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Cada método de serviço unário no  *\*arquivo. proto* resultará em dois métodos .net no tipo de cliente concreto gRPC para chamar o método: um método assíncrono e um método de bloqueio. Por exemplo, `GreeterClient` há duas maneiras de chamar `SayHello`:

* `GreeterClient.SayHelloAsync`-chama `Greeter.SayHello` o serviço de forma assíncrona. Pode ser esperado.
* `GreeterClient.SayHello`-chama `Greeter.SayHello` serviço e bloqueia até a conclusão. Não use em código assíncrono.

### <a name="server-streaming-call"></a>Chamada de streaming de servidor

Uma chamada de transmissão de servidor inicia com o cliente enviando uma mensagem de solicitação. `ResponseStream.MoveNext()`lê mensagens transmitidas do serviço. A chamada de streaming de servidor é `ResponseStream.MoveNext()` concluída `false`quando retorna.

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

Se você estiver usando C# 8 ou posterior, a `await foreach` sintaxe poderá ser usada para ler mensagens. O `IAsyncStreamReader<T>.ReadAllAsync()` método de extensão lê todas as mensagens do fluxo de resposta:

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

### <a name="client-streaming-call"></a>Chamada de streaming de cliente

Uma chamada de streaming de cliente inicia *sem* que o cliente envie uma mensagem. O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync`. Quando o cliente terminar, o envio `RequestStream.CompleteAsync` de mensagens deve ser chamado para notificar o serviço. A chamada é concluída quando o serviço retorna uma mensagem de resposta.

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

### <a name="bi-directional-streaming-call"></a>Chamada de streaming bidirecional

Uma chamada de streaming bidirecional é iniciada *sem* que o cliente envie uma mensagem. O cliente pode optar por enviar mensagens com `RequestStream.WriteAsync`. As mensagens transmitidas do serviço são acessíveis com `ResponseStream.MoveNext()` o `ResponseStream.ReadAllAsync()`ou o. A chamada de streaming bidirecional é concluída quando o `ResponseStream` não tem mais mensagens.

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

Durante uma chamada de streaming bidirecional, o cliente e o serviço podem enviar mensagens entre si a qualquer momento. A melhor lógica de cliente para interagir com uma chamada bidirecional varia dependendo da lógica do serviço.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
