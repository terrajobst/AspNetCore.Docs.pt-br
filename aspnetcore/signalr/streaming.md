---
title: Usar o streaming em SignalR do ASP.NET Core
author: bradygaster
description: Saiba como fluxos de valores de retorno de métodos de hub do servidor e consumir os fluxos usando os clientes .NET e JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: fb7183f7189d62c181f69ffdb170e3da25612919
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345581"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Usar o streaming em SignalR do ASP.NET Core

Por [Brennan Conroy](https://github.com/BrennanConroy)

SignalR do ASP.NET Core dá suporte a streaming valores de retorno dos métodos de servidor. Isso é útil para cenários em que os fragmentos de dados serão apresentadas ao longo do tempo. Quando um valor de retorno é transmitido ao cliente, cada fragmento é enviado ao cliente assim que ele se torna disponível, em vez de aguardar que todos os dados fiquem disponíveis.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="set-up-the-hub"></a>Configurar o hub

::: moniker range=">= aspnetcore-3.0"

Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, ou `Task<IAsyncEnumerable<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Um método de hub automaticamente se torna um método de hub streaming quando ele retorna um `ChannelReader<T>` ou um `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

No ASP.NET Core 3.0 ou posterior, os métodos de hub de streaming podem retornar `IAsyncEnumerable<T>` além `ChannelReader<T>`. A maneira mais simples de retornar `IAsyncEnumerable<T>` é, tornando o método de hub em um método de iterador assíncrono como o exemplo a seguir demonstra. Métodos de iterador assíncrono hub podem aceitar um `CancellationToken` parâmetro que será acionado quando o cliente cancela a assinatura do fluxo. Métodos de iterador assíncrono evitar problemas comuns com canais com facilidade como não retornar os `ChannelReader` cedo suficiente ou saindo do método sem concluir o `ChannelWriter`.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/sample/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

O exemplo a seguir mostra os conceitos básicos do fluxo de dados para o cliente usando canais. Sempre que um objeto é gravado o `ChannelWriter` esse objeto é enviado imediatamente para o cliente. No final, o `ChannelWriter` estiver concluído para dizer ao cliente o fluxo está fechado.

> [!NOTE]
> * Gravar o `ChannelWriter` em um thread em segundo plano e retorne o `ChannelReader` assim que possível. Outras chamadas de hub serão bloqueadas até que um `ChannelReader` é retornado.
> * Encapsular sua lógica em uma `try ... catch` e conclua o `Channel` em catch e fora de catch para garantir que o hub de invocação de método é concluída corretamente.

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

No ASP.NET Core 2.2 ou posterior, os métodos de hub de streaming podem aceitar um `CancellationToken` parâmetro que será acionado quando o cliente cancela a assinatura do fluxo. Use esse token para interromper a operação do servidor e liberar quaisquer recursos se o cliente se desconecta antes do final do fluxo.

::: moniker-end

## <a name="net-client"></a>Cliente .NET

O `StreamAsChannelAsync` método no `HubConnection` é usado para invocar um método de transmissão. Passe o nome do método de hub e argumentos definidos no método de hub para `StreamAsChannelAsync`. O parâmetro genérico em `StreamAsChannelAsync<T>` Especifica o tipo de objetos retornados pelo método de transmissão. Um `ChannelReader<T>` é retornada da invocação de fluxo e representa o fluxo no cliente. Para ler dados, um padrão comum é executar um loop sobre `WaitToReadAsync` e chamar `TryRead` quando os dados estiverem disponíveis. O loop terminará quando o fluxo foi fechado pelo servidor ou o token de cancelamento passado para `StreamAsChannelAsync` é cancelada.

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

## <a name="javascript-client"></a>Cliente JavaScript

Os clientes JavaScript chamar métodos de streaming em hubs usando `connection.stream`. O `stream` método aceita dois argumentos:

* O nome do método de hub. No exemplo a seguir, o nome do método de hub é `Counter`.
* Argumentos definidos no método de hub. No exemplo a seguir, os argumentos são: uma contagem do número de itens de fluxo para receber e o atraso entre itens de fluxo.

`connection.stream` Retorna um `IStreamResult` que contém um `subscribe` método. Passar uma `IStreamSubscriber` para `subscribe` e defina as `next`, `error`, e `complete` retornos de chamada para receber as notificações a `stream` invocação.

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Para terminar o fluxo do cliente, chame o `dispose` método em de `ISubscription` que é retornado do `subscribe` método. Chamar esse método fará com que o `CancellationToken` parâmetro do método de Hub (se você tiver fornecido um) a ser cancelada.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
## <a name="java-client"></a>Cliente Java
O cliente SignalR Java usa o `stream` método para invocar métodos de streaming. Ela aceita três ou mais argumentos:

* O tipo esperado de itens de fluxo 
* O nome do método de hub.
* Argumentos definidos no método de hub. 

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```
O `stream` método no `HubConnection` retorna um observável do tipo de item de fluxo. O tipo observável `subscribe` método é onde você define sua `onNext`, `onError` e `onCompleted` manipuladores.

::: moniker-end

## <a name="related-resources"></a>Recursos relacionados

* [Hubs](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
