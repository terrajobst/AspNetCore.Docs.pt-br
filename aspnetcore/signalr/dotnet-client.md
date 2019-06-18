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
# <a name="aspnet-core-signalr-net-client"></a>Cliente de .NET do SignalR do ASP.NET Core

A biblioteca de cliente .NET de SignalR do ASP.NET Core permite que você se comunicar com os hubs de SignalR em aplicativos .NET.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

O exemplo de código neste artigo é um aplicativo do WPF que usa o cliente .NET de SignalR do ASP.NET Core.

## <a name="install-the-signalr-net-client-package"></a>Instalar o pacote de cliente .NET do SignalR

O `Microsoft.AspNetCore.SignalR.Client` pacote é necessário para clientes .NET conectar-se aos hubs de SignalR. Para instalar a biblioteca de cliente, execute o seguinte comando na **Package Manager Console** janela:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a>Conectar a um hub

Para estabelecer uma conexão, cria uma `HubConnectionBuilder` e chamar `Build`. A URL do hub, protocolo, o tipo de transporte, nível de log, cabeçalhos e outras opções podem ser configuradas durante a criação de uma conexão. Configurar as opções necessárias, inserindo qualquer um dos `HubConnectionBuilder` métodos em `Build`. Iniciar a conexão com `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Lidar com a conexão perdida

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconectar-se automaticamente

O <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> pode ser configurado para se reconectar automaticamente usando o `WithAutomaticReconnect` método o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. Ele não se reconectar automaticamente por padrão.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Sem parâmetros, `WithAutomaticReconnect()` configura o cliente para aguardar 0, 2, 10 e 30 segundos respectivamente antes de tentar a cada tentativa de reconexão, parando depois de quatro tentativas com falha.

Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` será a transição para o `HubConnectionState.Reconnecting` de estado e acionar o `Reconnecting` eventos.  Isso fornece uma oportunidade para avisar os usuários que a conexão foi perdida e para desativar elementos da interface do usuário. Aplicativos não interativos podem iniciar o enfileiramento de mensagens ou descarte de mensagens.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Se o cliente se reconecta com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Connected` de estado e acionar o `Reconnected` eventos. Isso fornece uma oportunidade para informar os usuários a conexão ser restabelecida e remover da fila as mensagens na fila.

Como a conexão parece inteiramente nova para o servidor, um novo `ConnectionId` será fornecido para o `Reconnected` manipuladores de eventos.

> [!WARNING]
> O `Reconnected` manipulador de eventos `connectionId` parâmetro será nulo se o `HubConnection` foi configurado para [ignorar negociação](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` não configure o `HubConnection` para repetir as falhas de inicialização inicial, portanto, falhas de inicialização precisam ser feito manualmente:

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

Se o cliente não reconectar-se com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` será a transição para o `Disconnected` de estado e acionar o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> eventos. Isso fornece uma oportunidade para tentar reiniciar a conexão manualmente ou informar aos usuários que a conexão foi perdido permanentemente.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, `WithAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para esperar antes de começar a cada tentativa de reconexão.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconexão imediatamente depois que a conexão for perdida. Isso também é verdadeiro para a configuração padrão.

Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciado imediatamente em vez de aguardar de 2 segundos como apareceria na configuração padrão.

Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciado em 10 segundos, que é novamente como a configuração padrão.

O comportamento personalizado, em seguida, diverge novamente do comportamento padrão, interrompendo depois reconectar a terceira tentativa falha. A configuração de padrão seria ser um mais reconecta tentativa em outro 30 segundos.

Se você quiser ainda mais controle sobre o tempo e o número de automático se reconectar, as tentativas `WithAutomaticReconnect` aceita um objeto que implementa o `IRetryPolicy` interface, que tem um único método chamado `NextRetryDelay`.

`NextRetryDelay` usa um único argumento com o tipo `RetryContext`. O `RetryContext` tem três propriedades: `PreviousRetryCount`, `ElapsedTime` e `RetryReason` que são uma `long`, uma `TimeSpan` e um `Exception` , respectivamente. Antes da primeira tentativa de reconexão, ambos `PreviousRetryCount` e `ElapsedTime` será zero e o `RetryReason` será a exceção que causou a conexão serão perdidos. Após cada tentativa de repetição com falha, `PreviousRetryCount` será incrementado em um, `ElapsedTime` será atualizada para refletir a quantidade de tempo gasto se reconectar até agora e o `RetryReason` será a exceção que fez a última tentativa de reconexão falhe.

`NextRetryDelay` deve retornar qualquer um TimeSpan que representa o tempo a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de se reconectar.

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

Como alternativa, você pode escrever código que será reconectada seu cliente manualmente, conforme demonstrado [reconectar manualmente](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconectar manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes do 3.0, o cliente .NET para o SignalR não reconectados automaticamente. Você deve escrever código que será reconectada seu cliente manualmente.

::: moniker-end

Use o <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> eventos para responder a uma conexão perdida. Por exemplo, você talvez queira automatizar a reconexão.

O `Closed` evento requer um delegado que retorna um `Task`, que permite que o código assíncrono executar sem usar `async void`. Para satisfazer a assinatura do delegado em um `Closed` manipulador de eventos que é executado de forma síncrona, retorna `Task.CompletedTask`:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

O principal motivo para o suporte assíncrono é portanto, você pode reiniciar a conexão. Iniciar uma conexão é uma ação assíncrona.

Em um `Closed` manipulador que reinicia a conexão, considere aguardar algum atraso aleatório evitar sobrecarregar o servidor, conforme mostrado no exemplo a seguir:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Chamar métodos de hub do cliente

`InvokeAsync` chama métodos no hub. Passe o nome do método de hub e quaisquer argumentos definidos no método de hub para `InvokeAsync`. O SignalR é assíncrono, portanto, use `async` e `await` ao fazer as chamadas.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

O `InvokeAsync` método retorna um `Task` que conclui quando o método de servidor é retornado. O valor de retorno, se houver, é fornecido como o resultado do `Task`. Todas as exceções geradas pelo método no servidor de produzem uma falha `Task`. Use `await` sintaxe de espera para o método de servidor concluir e `try...catch` sintaxe para lidar com erros.

O `SendAsync` método retorna um `Task` que conclui quando a mensagem foi enviada ao servidor. Nenhum valor de retorno é fornecido uma vez que isso `Task` não espera até que o método de servidor seja concluída. Todas as exceções geradas no cliente ao enviar a mensagem de produzem uma falha `Task`. Use `await` e `try...catch` enviar de sintaxe para lidar com erros.

> [!NOTE]
> Se você estiver usando o serviço do Azure SignalR no *modo sem servidor*, você não pode chamar métodos de hub de um cliente. Para obter mais informações, consulte o [documentação do SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do hub

Definir métodos de hub de chamadas usando `connection.On` depois de criar, mas antes de iniciar a conexão.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

O código anterior no `connection.On` é executado quando o código do lado do servidor chama-o usando o `SendAsync` método.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Registro em log e tratamento de erros

Tratar erros com uma instrução try-catch. Inspecione o `Exception` objeto para determinar a ação apropriada a tomar depois de ocorrer um erro.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Documentação de sem servidor SignalR Service do Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
