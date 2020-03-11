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
# <a name="use-streaming-in-aspnet-core-opno-locsignalr"></a>Usar Streaming no ASP.NET Core SignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR dá suporte ao streaming do cliente para o servidor e do servidor para o cliente. Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo. Ao transmitir, cada fragmento é enviado ao cliente ou ao servidor assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR dá suporte a valores de retorno de transmissão de métodos de servidor. Isso é útil para cenários em que os fragmentos de dados chegam ao longo do tempo. Quando um valor de retorno é transmitido para o cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.

::: moniker-end

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Configurar um hub para streaming

::: moniker range=">= aspnetcore-3.0"

Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`ou `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um método de Hub se torna automaticamente um método de Hub de streaming quando retorna um <xref:System.Threading.Channels.ChannelReader%601> ou um `Task<ChannelReader<T>>`.

::: moniker-end

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente

::: moniker range=">= aspnetcore-3.0"

Os métodos de Hub de streaming podem retornar `IAsyncEnumerable<T>` além de `ChannelReader<T>`. A maneira mais simples de retornar `IAsyncEnumerable<T>` é fazendo com que o método de Hub seja um método de iterador assíncrono como demonstrado pelo exemplo a seguir. Os métodos iteradores assíncronos de Hub podem aceitar um parâmetro `CancellationToken` que é disparado quando o cliente cancela a assinatura do fluxo. Os métodos iteradores assíncronos evitam problemas comuns com canais, como não retornar o `ChannelReader` cedo o suficiente ou sair do método sem concluir a <xref:System.Threading.Channels.ChannelWriter`1>.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

O exemplo a seguir mostra as noções básicas de streaming de dados para o cliente usando canais. Sempre que um objeto é gravado no <xref:System.Threading.Channels.ChannelWriter%601>, o objeto é enviado imediatamente para o cliente. No final, a `ChannelWriter` é concluída para informar ao cliente que o fluxo está fechado.

> [!NOTE]
> Grave no `ChannelWriter<T>` em um thread em segundo plano e retorne o `ChannelReader` assim que possível. Outras invocações de Hub são bloqueadas até que um `ChannelReader` seja retornado.
>
> Ajustar a lógica em um `try ... catch`. Conclua a `Channel` no `catch` e fora do `catch` para verificar se a invocação do método de Hub foi concluída corretamente.

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

Os métodos de Hub de streaming de servidor para cliente podem aceitar um `CancellationToken` parâmetro que é disparado quando o cliente cancela a assinatura do fluxo. Use esse token para interromper a operação do servidor e liberar todos os recursos se o cliente se desconectar antes do final do fluxo.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente para servidor

Um método de Hub se torna automaticamente um método de Hub de streaming de cliente para servidor quando ele aceita um ou mais objetos do tipo <xref:System.Threading.Channels.ChannelReader%601> ou <xref:System.Collections.Generic.IAsyncEnumerable%601>. O exemplo a seguir mostra as noções básicas de leitura de dados de streaming enviados do cliente. Sempre que o cliente grava no <xref:System.Threading.Channels.ChannelWriter%601>, os dados são gravados no `ChannelReader` no servidor do qual o método de Hub está lendo.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

Segue um <xref:System.Collections.Generic.IAsyncEnumerable%601> versão do método.

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

## <a name="net-client"></a>Cliente .NET

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente


::: moniker range=">= aspnetcore-3.0"

Os métodos `StreamAsync` e `StreamAsChannelAsync` no `HubConnection` são usados para invocar métodos de transmissão de servidor para cliente. Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsync` ou `StreamAsChannelAsync`. O parâmetro Generic em `StreamAsync<T>` e `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming. Um objeto do tipo `IAsyncEnumerable<T>` ou `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.

Um exemplo `StreamAsync` que retorna `IAsyncEnumerable<int>`:

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

Um exemplo de `StreamAsChannelAsync` correspondente que retorna `ChannelReader<int>`:

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

O método `StreamAsChannelAsync` em `HubConnection` é usado para invocar um método de transmissão de servidor para cliente. Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync`. O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming. Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.

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

O método `StreamAsChannelAsync` em `HubConnection` é usado para invocar um método de transmissão de servidor para cliente. Passe o nome do método de Hub e os argumentos definidos no método de Hub para `StreamAsChannelAsync`. O parâmetro Generic em `StreamAsChannelAsync<T>` especifica o tipo de objetos retornados pelo método de streaming. Um `ChannelReader<T>` é retornado da invocação de fluxo e representa o fluxo no cliente.

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

### <a name="client-to-server-streaming"></a>Streaming de cliente para servidor

Há duas maneiras de invocar um método de Hub de transmissão de cliente para servidor do cliente .NET. Você pode passar um `IAsyncEnumerable<T>` ou um `ChannelReader` como um argumento para `SendAsync`, `InvokeAsync`ou `StreamAsChannelAsync`, dependendo do método de Hub invocado.

Sempre que os dados são gravados no objeto `IAsyncEnumerable` ou `ChannelWriter`, o método Hub no servidor recebe um novo item com os dados do cliente.

Se estiver usando um objeto `IAsyncEnumerable`, o fluxo terminará depois que o método que retorna os itens de fluxo for encerrado.

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

Ou, se você estiver usando uma `ChannelWriter`, você concluirá o canal com `channel.Writer.Complete()`:

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Cliente JavaScript

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente

Os clientes JavaScript chamam métodos de transmissão de servidor para cliente em hubs com `connection.stream`. O método `stream` aceita dois argumentos:

* O nome do método de Hub. No exemplo a seguir, o nome do método de Hub é `Counter`.
* Argumentos definidos no método Hub. No exemplo a seguir, os argumentos são uma contagem para o número de itens de fluxo a serem recebidos e o atraso entre itens de fluxo.

`connection.stream` retorna um `IStreamResult`, que contém um método `subscribe`. Passe um `IStreamSubscriber` para `subscribe` e defina os retornos de chamada `next`, `error`e `complete` para receber notificações da invocação de `stream`.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Para finalizar o fluxo do cliente, chame o método `dispose` no `ISubscription` retornado do método `subscribe`. Chamar esse método causa o cancelamento do parâmetro `CancellationToken` do método Hub, se você tiver fornecido um.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Para finalizar o fluxo do cliente, chame o método `dispose` no `ISubscription` retornado do método `subscribe`.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente para servidor

Os clientes JavaScript chamam métodos de transmissão de cliente para servidor em hubs, passando um `Subject` como um argumento para `send`, `invoke`ou `stream`, dependendo do método de Hub invocado. A `Subject` é uma classe parecida com uma `Subject`. Por exemplo, em RxJS, você pode usar a classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) dessa biblioteca.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Chamar `subject.next(item)` com um item grava o item no fluxo e o método Hub recebe o item no servidor.

Para encerrar o fluxo, chame `subject.complete()`.

## <a name="java-client"></a>Cliente Java

### <a name="server-to-client-streaming"></a>Streaming de servidor para cliente

O cliente Java SignalR usa o método `stream` para invocar métodos de streaming. `stream` aceita três ou mais argumentos:

* O tipo esperado dos itens de fluxo.
* O nome do método de Hub.
* Argumentos definidos no método Hub.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

O método `stream` em `HubConnection` retorna um observável do tipo de item de fluxo. O método `subscribe` do tipo observável é onde `onNext`, `onError` e manipuladores de `onCompleted` são definidos.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
