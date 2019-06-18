---
title: Cliente de .NET do SignalR do ASP.NET Core
author: bradygaster
description: Informações sobre o cliente do .NET Core ASP.NET SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 97c553874cb1e4b678fa0e5cd65074f135193861
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153126"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="5cf55-103">Cliente de .NET do SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5cf55-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="5cf55-104">A biblioteca de cliente .NET de SignalR do ASP.NET Core permite que você se comunicar com os hubs de SignalR em aplicativos .NET.</span><span class="sxs-lookup"><span data-stu-id="5cf55-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="5cf55-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5cf55-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5cf55-106">O exemplo de código neste artigo é um aplicativo do WPF que usa o cliente .NET de SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5cf55-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="5cf55-107">Instalar o pacote de cliente .NET do SignalR</span><span class="sxs-lookup"><span data-stu-id="5cf55-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="5cf55-108">O `Microsoft.AspNetCore.SignalR.Client` pacote é necessário para clientes .NET conectar-se aos hubs de SignalR.</span><span class="sxs-lookup"><span data-stu-id="5cf55-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="5cf55-109">Para instalar a biblioteca de cliente, execute o seguinte comando na **Package Manager Console** janela:</span><span class="sxs-lookup"><span data-stu-id="5cf55-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="5cf55-110">Conectar a um hub</span><span class="sxs-lookup"><span data-stu-id="5cf55-110">Connect to a hub</span></span>

<span data-ttu-id="5cf55-111">Para estabelecer uma conexão, cria uma `HubConnectionBuilder` e chamar `Build`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="5cf55-112">A URL do hub, protocolo, o tipo de transporte, nível de log, cabeçalhos e outras opções podem ser configuradas durante a criação de uma conexão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="5cf55-113">Configurar as opções necessárias, inserindo qualquer um dos `HubConnectionBuilder` métodos em `Build`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="5cf55-114">Iniciar a conexão com `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="5cf55-115">Lidar com a conexão perdida</span><span class="sxs-lookup"><span data-stu-id="5cf55-115">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="5cf55-116">Reconectar-se automaticamente</span><span class="sxs-lookup"><span data-stu-id="5cf55-116">Automatically reconnect</span></span>

<span data-ttu-id="5cf55-117">O <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> pode ser configurado para se reconectar automaticamente usando o `WithAutomaticReconnect` método o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5cf55-117">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="5cf55-118">Ele não se reconectar automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-118">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="5cf55-119">Sem parâmetros, `WithAutomaticReconnect()` configura o cliente para aguardar 0, 2, 10 e 30 segundos respectivamente antes de tentar a cada tentativa de reconexão, parando depois de quatro tentativas com falha.</span><span class="sxs-lookup"><span data-stu-id="5cf55-119">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="5cf55-120">Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` será a transição para o `HubConnectionState.Reconnecting` de estado e acionar o `Reconnecting` eventos.</span><span class="sxs-lookup"><span data-stu-id="5cf55-120">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="5cf55-121">Isso fornece uma oportunidade para avisar os usuários que a conexão foi perdida e para desativar elementos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="5cf55-121">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="5cf55-122">Aplicativos não interativos podem iniciar o enfileiramento de mensagens ou descarte de mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cf55-122">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="5cf55-123">Se o cliente se reconecta com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Connected` de estado e acionar o `Reconnected` eventos.</span><span class="sxs-lookup"><span data-stu-id="5cf55-123">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="5cf55-124">Isso fornece uma oportunidade para informar os usuários a conexão ser restabelecida e remover da fila as mensagens na fila.</span><span class="sxs-lookup"><span data-stu-id="5cf55-124">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="5cf55-125">Como a conexão parece inteiramente nova para o servidor, um novo `ConnectionId` será fornecido para o `Reconnected` manipuladores de eventos.</span><span class="sxs-lookup"><span data-stu-id="5cf55-125">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="5cf55-126">O `Reconnected` manipulador de eventos `connectionId` parâmetro será nulo se o `HubConnection` foi configurado para [ignorar negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="5cf55-126">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="5cf55-127">`WithAutomaticReconnect()` não configure o `HubConnection` para repetir as falhas de inicialização inicial, portanto, falhas de inicialização precisam ser feito manualmente:</span><span class="sxs-lookup"><span data-stu-id="5cf55-127">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="5cf55-128">Se o cliente não reconectar-se com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` será a transição para o `Disconnected` de estado e acionar o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> eventos.</span><span class="sxs-lookup"><span data-stu-id="5cf55-128">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="5cf55-129">Isso fornece uma oportunidade para tentar reiniciar a conexão manualmente ou informar aos usuários que a conexão foi perdido permanentemente.</span><span class="sxs-lookup"><span data-stu-id="5cf55-129">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="5cf55-130">Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, `WithAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para esperar antes de começar a cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-130">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="5cf55-131">O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconexão imediatamente depois que a conexão for perdida.</span><span class="sxs-lookup"><span data-stu-id="5cf55-131">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="5cf55-132">Isso também é verdadeiro para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-132">This is also true for the default configuration.</span></span>

<span data-ttu-id="5cf55-133">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciado imediatamente em vez de aguardar de 2 segundos como apareceria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-133">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="5cf55-134">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciado em 10 segundos, que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-134">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="5cf55-135">O comportamento personalizado, em seguida, diverge novamente do comportamento padrão, interrompendo depois reconectar a terceira tentativa falha.</span><span class="sxs-lookup"><span data-stu-id="5cf55-135">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="5cf55-136">A configuração de padrão seria ser um mais reconecta tentativa em outro 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="5cf55-136">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="5cf55-137">Se você quiser ainda mais controle sobre o tempo e o número de automático se reconectar, as tentativas `WithAutomaticReconnect` aceita um objeto que implementa o `IRetryPolicy` interface, que tem um único método chamado `NextRetryDelay`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-137">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="5cf55-138">`NextRetryDelay` usa um único argumento com o tipo `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-138">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="5cf55-139">O `RetryContext` tem três propriedades: `PreviousRetryCount`, `ElapsedTime` e `RetryReason` que são uma `long`, uma `TimeSpan` e um `Exception` , respectivamente.</span><span class="sxs-lookup"><span data-stu-id="5cf55-139">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="5cf55-140">Antes da primeira tentativa de reconexão, ambos `PreviousRetryCount` e `ElapsedTime` será zero e o `RetryReason` será a exceção que causou a conexão serão perdidos.</span><span class="sxs-lookup"><span data-stu-id="5cf55-140">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="5cf55-141">Após cada tentativa de repetição com falha, `PreviousRetryCount` será incrementado em um, `ElapsedTime` será atualizada para refletir a quantidade de tempo gasto se reconectar até agora e o `RetryReason` será a exceção que fez a última tentativa de reconexão falhe.</span><span class="sxs-lookup"><span data-stu-id="5cf55-141">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="5cf55-142">`NextRetryDelay` deve retornar qualquer um TimeSpan que representa o tempo a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de se reconectar.</span><span class="sxs-lookup"><span data-stu-id="5cf55-142">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="5cf55-143">Como alternativa, você pode escrever código que será reconectada seu cliente manualmente, conforme demonstrado [reconectar manualmente](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="5cf55-143">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="5cf55-144">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="5cf55-144">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="5cf55-145">Antes do 3.0, o cliente .NET para o SignalR não reconectados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="5cf55-145">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="5cf55-146">Você deve escrever código que será reconectada seu cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="5cf55-146">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="5cf55-147">Use o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> eventos para responder a uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="5cf55-147">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="5cf55-148">Por exemplo, você talvez queira automatizar a reconexão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-148">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="5cf55-149">O `Closed` evento requer um delegado que retorna um `Task`, que permite que o código assíncrono executar sem usar `async void`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-149">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="5cf55-150">Para satisfazer a assinatura do delegado em um `Closed` manipulador de eventos que é executado de forma síncrona, retorna `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="5cf55-150">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="5cf55-151">O principal motivo para o suporte assíncrono é portanto, você pode reiniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-151">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="5cf55-152">Iniciar uma conexão é uma ação assíncrona.</span><span class="sxs-lookup"><span data-stu-id="5cf55-152">Starting a connection is an async action.</span></span>

<span data-ttu-id="5cf55-153">Em um `Closed` manipulador que reinicia a conexão, considere aguardar algum atraso aleatório evitar sobrecarregar o servidor, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5cf55-153">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="5cf55-154">Chamar métodos de hub do cliente</span><span class="sxs-lookup"><span data-stu-id="5cf55-154">Call hub methods from client</span></span>

<span data-ttu-id="5cf55-155">`InvokeAsync` chama métodos no hub.</span><span class="sxs-lookup"><span data-stu-id="5cf55-155">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="5cf55-156">Passe o nome do método de hub e quaisquer argumentos definidos no método de hub para `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-156">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="5cf55-157">O SignalR é assíncrono, portanto, use `async` e `await` ao fazer as chamadas.</span><span class="sxs-lookup"><span data-stu-id="5cf55-157">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="5cf55-158">O `InvokeAsync` método retorna um `Task` que conclui quando o método de servidor é retornado.</span><span class="sxs-lookup"><span data-stu-id="5cf55-158">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="5cf55-159">O valor de retorno, se houver, é fornecido como o resultado do `Task`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-159">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="5cf55-160">Todas as exceções geradas pelo método no servidor de produzem uma falha `Task`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-160">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="5cf55-161">Use `await` sintaxe de espera para o método de servidor concluir e `try...catch` sintaxe para lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="5cf55-161">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="5cf55-162">O `SendAsync` método retorna um `Task` que conclui quando a mensagem foi enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="5cf55-162">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="5cf55-163">Nenhum valor de retorno é fornecido uma vez que isso `Task` não espera até que o método de servidor seja concluída.</span><span class="sxs-lookup"><span data-stu-id="5cf55-163">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="5cf55-164">Todas as exceções geradas no cliente ao enviar a mensagem de produzem uma falha `Task`.</span><span class="sxs-lookup"><span data-stu-id="5cf55-164">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="5cf55-165">Use `await` e `try...catch` enviar de sintaxe para lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="5cf55-165">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="5cf55-166">Se você estiver usando o serviço do Azure SignalR no *modo sem servidor*, você não pode chamar métodos de hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="5cf55-166">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="5cf55-167">Para obter mais informações, consulte o [documentação do SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="5cf55-167">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="5cf55-168">Chamar métodos de cliente do hub</span><span class="sxs-lookup"><span data-stu-id="5cf55-168">Call client methods from hub</span></span>

<span data-ttu-id="5cf55-169">Definir métodos de hub de chamadas usando `connection.On` depois de criar, mas antes de iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="5cf55-169">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="5cf55-170">O código anterior no `connection.On` é executado quando o código do lado do servidor chama-o usando o `SendAsync` método.</span><span class="sxs-lookup"><span data-stu-id="5cf55-170">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="5cf55-171">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="5cf55-171">Error handling and logging</span></span>

<span data-ttu-id="5cf55-172">Tratar erros com uma instrução try-catch.</span><span class="sxs-lookup"><span data-stu-id="5cf55-172">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="5cf55-173">Inspecione o `Exception` objeto para determinar a ação apropriada a tomar depois de ocorrer um erro.</span><span class="sxs-lookup"><span data-stu-id="5cf55-173">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="5cf55-174">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5cf55-174">Additional resources</span></span>

* [<span data-ttu-id="5cf55-175">Hubs</span><span class="sxs-lookup"><span data-stu-id="5cf55-175">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5cf55-176">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="5cf55-176">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="5cf55-177">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="5cf55-177">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="5cf55-178">Documentação de sem servidor SignalR Service do Azure</span><span class="sxs-lookup"><span data-stu-id="5cf55-178">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
