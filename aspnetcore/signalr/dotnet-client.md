---
title: ASP.NET Core SignalR cliente .NET
author: bradygaster
description: Informações sobre o cliente ASP.NET Core SignalR .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 28e8fcf808406cd0251ba94e2ef97ab04841fcd0
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963962"
---
# <a name="aspnet-core-opno-locsignalr-net-client"></a><span data-ttu-id="b1aef-103">ASP.NET Core SignalR cliente .NET</span><span class="sxs-lookup"><span data-stu-id="b1aef-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="b1aef-104">O ASP.NET Core SignalR biblioteca de cliente .NET permite que você se comunique com hubs de SignalR de aplicativos .NET.</span><span class="sxs-lookup"><span data-stu-id="b1aef-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="b1aef-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b1aef-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b1aef-106">O exemplo de código neste artigo é um aplicativo do WPF que usa o cliente ASP.NET Core SignalR .NET.</span><span class="sxs-lookup"><span data-stu-id="b1aef-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-opno-locsignalr-net-client-package"></a><span data-ttu-id="b1aef-107">Instalar o pacote do cliente .NET SignalR</span><span class="sxs-lookup"><span data-stu-id="b1aef-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="b1aef-108">O [Microsoft. AspNetCore.SignalR. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)O pacote do cliente é necessário para que os clientes .net se conectem a hubs de SignalR.</span><span class="sxs-lookup"><span data-stu-id="b1aef-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b1aef-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b1aef-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b1aef-110">Para instalar a biblioteca de cliente, execute o seguinte comando na janela do **console do Gerenciador de pacotes** :</span><span class="sxs-lookup"><span data-stu-id="b1aef-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b1aef-111">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b1aef-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b1aef-112">Para instalar a biblioteca de cliente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="b1aef-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="b1aef-113">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="b1aef-113">Connect to a hub</span></span>

<span data-ttu-id="b1aef-114">Para estabelecer uma conexão, crie um `HubConnectionBuilder` e chame `Build`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="b1aef-115">A URL do Hub, o protocolo, o tipo de transporte, o nível de log, os cabeçalhos e outras opções podem ser configurados durante a criação de uma conexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="b1aef-116">Configure as opções necessárias inserindo qualquer um dos métodos de `HubConnectionBuilder` em `Build`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="b1aef-117">Inicie a conexão com `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="b1aef-118">Tratar conexão perdida</span><span class="sxs-lookup"><span data-stu-id="b1aef-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="b1aef-119">Reconectar automaticamente</span><span class="sxs-lookup"><span data-stu-id="b1aef-119">Automatically reconnect</span></span>

<span data-ttu-id="b1aef-120">O <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> pode ser configurado para reconectar-se automaticamente usando o método `WithAutomaticReconnect` no <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b1aef-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="b1aef-121">Ele não se reconectará automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="b1aef-122">Sem parâmetros, `WithAutomaticReconnect()` configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.</span><span class="sxs-lookup"><span data-stu-id="b1aef-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="b1aef-123">Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o estado de `HubConnectionState.Reconnecting` e acionará o evento de `Reconnecting`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="b1aef-124">Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="b1aef-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="b1aef-125">Aplicativos não interativos podem começar a enfileirar ou descartar mensagens.</span><span class="sxs-lookup"><span data-stu-id="b1aef-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="b1aef-126">Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o estado de `Connected` e acionará o evento de `Reconnected`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="b1aef-127">Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida e remover todas as mensagens na fila.</span><span class="sxs-lookup"><span data-stu-id="b1aef-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="b1aef-128">Como a conexão é totalmente nova no servidor, um novo `ConnectionId` será fornecido para os manipuladores de eventos `Reconnected`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="b1aef-129">O parâmetro `connectionId` do manipulador de eventos `Reconnected` será nulo se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="b1aef-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="b1aef-130">`WithAutomaticReconnect()` não configurará o `HubConnection` para repetir as falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:</span><span class="sxs-lookup"><span data-stu-id="b1aef-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="b1aef-131">Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o estado de `Disconnected` e acionará o evento de <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>.</span><span class="sxs-lookup"><span data-stu-id="b1aef-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="b1aef-132">Isso oferece uma oportunidade de tentar reiniciar a conexão manualmente ou informar aos usuários que a conexão foi permanentemente perdida.</span><span class="sxs-lookup"><span data-stu-id="b1aef-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="b1aef-133">Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, `WithAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="b1aef-134">O exemplo anterior configura a `HubConnection` para iniciar a tentativa de reconexão imediatamente após a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="b1aef-135">Isso também é verdadeiro para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="b1aef-136">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="b1aef-137">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="b1aef-138">O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira falha de tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="b1aef-139">Na configuração padrão, haveria mais uma tentativa de reconexão em outros 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="b1aef-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="b1aef-140">Se você quiser ter ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, `WithAutomaticReconnect` aceitará um objeto que implementa a interface `IRetryPolicy`, que tem um único método chamado `NextRetryDelay`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="b1aef-141">`NextRetryDelay` usa um único argumento com o tipo `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="b1aef-142">O `RetryContext` tem três propriedades: `PreviousRetryCount`, `ElapsedTime` e `RetryReason` que são um `long`, um `TimeSpan` e um `Exception` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="b1aef-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="b1aef-143">Antes da primeira tentativa de reconexão, `PreviousRetryCount` e `ElapsedTime` serão zero e a `RetryReason` será a exceção que fez com que a conexão fosse perdida.</span><span class="sxs-lookup"><span data-stu-id="b1aef-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="b1aef-144">Após cada tentativa de repetição com falha, `PreviousRetryCount` será incrementado em uma, `ElapsedTime` será atualizado para refletir o tempo gasto se reconectando até agora, e a `RetryReason` será a exceção que causou a falha da última tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="b1aef-145">`NextRetryDelay` deve retornar um TimeSpan que representa o tempo de espera antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de reconectar.</span><span class="sxs-lookup"><span data-stu-id="b1aef-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.Next() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="b1aef-146">Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="b1aef-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="b1aef-147">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="b1aef-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="b1aef-148">Antes do 3,0, o cliente .NET para SignalR não se reconecta automaticamente.</span><span class="sxs-lookup"><span data-stu-id="b1aef-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="b1aef-149">Você deve escrever um código que reconectará o cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="b1aef-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="b1aef-150">Use o evento <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> para responder a uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="b1aef-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="b1aef-151">Por exemplo, talvez você queira automatizar a reconexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="b1aef-152">O evento `Closed` requer um delegado que retorne um `Task`, que permite que o código assíncrono seja executado sem usar `async void`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="b1aef-153">Para satisfazer a assinatura de delegado em um manipulador de eventos `Closed` executado de forma síncrona, retorne `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="b1aef-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="b1aef-154">O principal motivo para o suporte assíncrono é que você pode reiniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="b1aef-155">Iniciar uma conexão é uma ação assíncrona.</span><span class="sxs-lookup"><span data-stu-id="b1aef-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="b1aef-156">Em um manipulador de `Closed` que reinicia a conexão, considere aguardar um atraso aleatório para evitar sobrecarregar o servidor, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b1aef-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="b1aef-157">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="b1aef-157">Call hub methods from client</span></span>

<span data-ttu-id="b1aef-158">`InvokeAsync` chama métodos no Hub.</span><span class="sxs-lookup"><span data-stu-id="b1aef-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="b1aef-159">Passe o nome do método de Hub e quaisquer argumentos definidos no método de Hub para `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> SignalR<span data-ttu-id="b1aef-160"> é assíncrona, portanto, use `async` e `await` ao fazer as chamadas.</span><span class="sxs-lookup"><span data-stu-id="b1aef-160"> is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="b1aef-161">O método `InvokeAsync` retorna uma `Task` que é concluída quando o método de servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="b1aef-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="b1aef-162">O valor de retorno, se houver, é fornecido como o resultado da `Task`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="b1aef-163">Todas as exceções geradas pelo método no servidor produzem um `Task`com falha.</span><span class="sxs-lookup"><span data-stu-id="b1aef-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="b1aef-164">Use a sintaxe `await` para aguardar a conclusão do método de servidor e `try...catch` sintaxe para lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="b1aef-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="b1aef-165">O método `SendAsync` retorna uma `Task` que é concluída quando a mensagem é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="b1aef-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="b1aef-166">Nenhum valor de retorno é fornecido, pois essa `Task` não aguarda até que o método de servidor seja concluído.</span><span class="sxs-lookup"><span data-stu-id="b1aef-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="b1aef-167">Todas as exceções geradas no cliente durante o envio da mensagem produzem um `Task`com falha.</span><span class="sxs-lookup"><span data-stu-id="b1aef-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="b1aef-168">Use a sintaxe `await` e `try...catch` para lidar com erros de envio.</span><span class="sxs-lookup"><span data-stu-id="b1aef-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="b1aef-169">Se você estiver usando o serviço de SignalR do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="b1aef-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="b1aef-170">Para obter mais informações, consulte a [documentação do serviço deSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="b1aef-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="b1aef-171">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="b1aef-171">Call client methods from hub</span></span>

<span data-ttu-id="b1aef-172">Defina os métodos que o Hub chama usando `connection.On` após a compilação, mas antes de iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="b1aef-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="b1aef-173">O código anterior em `connection.On` é executado quando o código do lado do servidor o chama usando o método `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="b1aef-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="b1aef-174">Tratamento de erros e registro em log</span><span class="sxs-lookup"><span data-stu-id="b1aef-174">Error handling and logging</span></span>

<span data-ttu-id="b1aef-175">Tratar erros com uma instrução try-catch.</span><span class="sxs-lookup"><span data-stu-id="b1aef-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="b1aef-176">Inspecione o objeto `Exception` para determinar a ação apropriada a ser tomada após ocorrer um erro.</span><span class="sxs-lookup"><span data-stu-id="b1aef-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="b1aef-177">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b1aef-177">Additional resources</span></span>

* [<span data-ttu-id="b1aef-178">Hubs</span><span class="sxs-lookup"><span data-stu-id="b1aef-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b1aef-179">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="b1aef-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="b1aef-180">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="b1aef-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* <span data-ttu-id="b1aef-181">[Documentação sem servidor do serviço SignalR do Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="b1aef-181">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
