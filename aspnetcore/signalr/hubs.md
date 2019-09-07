---
title: Usar hubs no sinalizador ASP.NET Core
author: bradygaster
description: Saiba como usar hubs no ASP.NET Core Signalr.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: 4922d6d780b18727d3ac181b94dbf75458d74fe6
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746519"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Usar hubs no Signalr para ASP.NET Core

Por [Rachel appel](https://twitter.com/rachelappel) e [Kevin Griffin](https://twitter.com/1kevgriff)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>O que é um Hub do Signalr

A API de hubs de Signalr permite que você chame métodos em clientes conectados do servidor. No código do servidor, você define os métodos que são chamados pelo cliente. No código do cliente, você define os métodos que são chamados do servidor. O signalr cuida de tudo por trás dos bastidores que possibilitam a comunicação de cliente para servidor e servidor para cliente em tempo real possível.

## <a name="configure-signalr-hubs"></a>Configurar hubs de sinalização

O middleware Signalr requer alguns serviços, que são configurados chamando `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Ao adicionar a funcionalidade do signalr a um aplicativo ASP.NET Core, configure as `endpoint.MapHub` `Startup.Configure` rotas do signalr chamando no `app.UseEndpoints` retorno de chamada do método.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Ao adicionar a funcionalidade do signalr a um aplicativo ASP.NET Core, configure as `app.UseSignalR` `Startup.Configure` rotas do signalr chamando no método.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Criar e usar hubs

Crie um hub declarando uma classe que herda `Hub`de e adicione métodos públicos a ele. Os clientes podem chamar métodos que são definidos `public`como.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Você pode especificar um tipo de retorno e parâmetros, incluindo tipos complexos e matrizes, como faria em C# qualquer método. O signalr lida com a serialização e a desserialização de objetos e matrizes complexos em seus parâmetros e valores de retorno.

> [!NOTE]
> Os hubs são transitórios:
>
> * Não armazene o estado em uma propriedade na classe Hub. Cada chamada de método de Hub é executada em uma nova instância de Hub.
> * Use `await` ao chamar métodos assíncronos que dependem do Hub permanecendo ativo. Por exemplo, um método como `Clients.All.SendAsync(...)` pode falhar se ele for chamado sem `await` e o método de Hub for concluído antes `SendAsync` de ser concluído.

## <a name="the-context-object"></a>O objeto de contexto

A `Hub` classe tem uma `Context` propriedade que contém as seguintes propriedades com informações sobre a conexão:

| Propriedade | Descrição |
| ------ | ----------- |
| `ConnectionId` | Obtém a ID exclusiva da conexão, atribuída pelo Signalr. Há uma ID de conexão para cada conexão.|
| `UserIdentifier` | Obtém o [identificador de usuário](xref:signalr/groups). Por padrão, o signalr usa `ClaimTypes.NameIdentifier` o `ClaimsPrincipal` do associado à conexão como o identificador de usuário. |
| `User` | Obtém o `ClaimsPrincipal` associado ao usuário atual. |
| `Items` | Obtém uma coleção de chave/valor que pode ser usada para compartilhar dados dentro do escopo desta conexão. Os dados podem ser armazenados nessa coleção e serão mantidos para a conexão entre invocações de método de Hub diferentes. |
| `Features` | Obtém a coleção de recursos disponíveis na conexão. Por enquanto, essa coleção não é necessária na maioria dos cenários, portanto, ela ainda não está documentada em detalhes. |
| `ConnectionAborted` | Obtém um `CancellationToken` que notifica quando a conexão é anulada. |

`Hub.Context`também contém os seguintes métodos:

| Método | Descrição |
| ------ | ----------- |
| `GetHttpContext` | Retorna o `HttpContext` para a conexão ou `null` se a conexão não estiver associada a uma solicitação HTTP. Para conexões HTTP, você pode usar esse método para obter informações como cabeçalhos HTTP e cadeias de caracteres de consulta. |
| `Abort` | Anula a conexão. |

## <a name="the-clients-object"></a>O objeto clients

A `Hub` classe tem uma `Clients` propriedade que contém as seguintes propriedades para comunicação entre o servidor e o cliente:

| Propriedade | Descrição |
| ------ | ----------- |
| `All` | Chama um método em todos os clientes conectados |
| `Caller` | Chama um método no cliente que invocou o método de Hub |
| `Others` | Chama um método em todos os clientes conectados, exceto o cliente que invocou o método |

`Hub.Clients`também contém os seguintes métodos:

| Método | Descrição |
| ------ | ----------- |
| `AllExcept` | Chama um método em todos os clientes conectados, exceto para as conexões especificadas |
| `Client` | Chama um método em um cliente conectado específico |
| `Clients` | Chama um método em clientes conectados específicos |
| `Group` | Chama um método em todas as conexões no grupo especificado  |
| `GroupExcept` | Chama um método em todas as conexões no grupo especificado, exceto as conexões especificadas |
| `Groups` | Chama um método em vários grupos de conexões  |
| `OthersInGroup` | Chama um método em um grupo de conexões, excluindo o cliente que invocou o método de Hub  |
| `User` | Chama um método em todas as conexões associadas a um usuário específico |
| `Users` | Chama um método em todas as conexões associadas aos usuários especificados |

Cada propriedade ou método nas tabelas anteriores retorna um objeto com um `SendAsync` método. O `SendAsync` método permite que você forneça o nome e os parâmetros do método de cliente a ser chamado.

## <a name="send-messages-to-clients"></a>Enviar mensagens para clientes

Para fazer chamadas para clientes específicos, use as propriedades do `Clients` objeto. No exemplo a seguir, há três métodos de Hub:

* `SendMessage`envia uma mensagem para todos os clientes conectados, `Clients.All`usando.
* `SendMessageToCaller`envia uma mensagem de volta ao chamador, usando `Clients.Caller`.
* `SendMessageToGroups`envia uma mensagem para todos os clientes no `SignalR Users` grupo.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Hubs com rigidez de tipos

Uma desvantagem de usar `SendAsync` o é que ele se baseia em uma cadeia de caracteres mágica para especificar o método de cliente a ser chamado. Isso deixa o código aberto para erros de tempo de execução se o nome do método for digitado incorretamente ou ausente no cliente.

Uma alternativa ao uso `SendAsync` do `Hub` é digitar fortemente com <xref:Microsoft.AspNetCore.SignalR.Hub%601>. No exemplo a seguir, os `ChatHub` métodos de cliente foram extraídos para uma interface chamada `IChatClient`.

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Essa interface pode ser usada para refatorar o exemplo `ChatHub` anterior.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

O `Hub<IChatClient>` uso de habilita a verificação de tempo de compilação dos métodos de cliente. Isso evita problemas causados pelo uso de cadeias de caracteres mágicos, pois `Hub<T>` o só pode fornecer acesso aos métodos definidos na interface.

Usar um tipo `Hub<T>` fortemente desabilita a capacidade de usar `SendAsync`. Todos os métodos definidos na interface ainda podem ser definidos como assíncronos. Na verdade, cada um desses métodos deve retornar um `Task`. Como é uma interface, não use a `async` palavra-chave. Por exemplo:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> O `Async` sufixo não é removido do nome do método. A menos que o método de cliente `.on('MyMethodAsync')`seja definido com, `MyMethodAsync` você não deve usar como um nome.

## <a name="change-the-name-of-a-hub-method"></a>Alterar o nome de um método de Hub

Por padrão, um nome de método de Hub de servidor é o nome do método .NET. No entanto, você pode usar o atributo [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) para alterar esse padrão e especificar manualmente um nome para o método. O cliente deve usar esse nome, em vez do nome do método .NET, ao invocar o método.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Manipular eventos para uma conexão

A API de hubs do signalr `OnConnectedAsync` fornece `OnDisconnectedAsync` os métodos virtuais e para gerenciar e controlar as conexões. Substitua o `OnConnectedAsync` método virtual para executar ações quando um cliente se conectar ao Hub, como adicioná-lo a um grupo.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Substitua o `OnDisconnectedAsync` método virtual para executar ações quando um cliente se desconectar. Se o cliente se desconectar intencionalmente (chamando `connection.stop()`, por exemplo), o `exception` parâmetro será `null`. No entanto, se o cliente for desconectado devido a um erro (como uma falha de rede `exception` ), o parâmetro conterá uma exceção que descreve a falha.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>Tratar erros

As exceções geradas em seus métodos de Hub são enviadas ao cliente que invocou o método. No cliente JavaScript, o `invoke` método retorna uma promessa de [JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Quando o cliente recebe um erro com um manipulador anexado à promessa usando `catch`, ele é invocado e passado como um objeto JavaScript. `Error`

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Se o Hub lançar uma exceção, as conexões não serão fechadas. Por padrão, o Signalr retorna uma mensagem de erro genérica para o cliente. Por exemplo:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Exceções inesperadas geralmente contêm informações confidenciais, como o nome de um servidor de banco de dados em uma exceção disparada quando a conexão de banco de dados falha. O signalr não expõe essas mensagens de erro detalhadas por padrão como uma medida de segurança. Consulte o [artigo considerações sobre segurança](xref:signalr/security#exceptions) para obter mais informações sobre por que os detalhes da exceção são suprimidos.

Se você tiver uma condição *excepcional que deseja* propagar para o cliente, poderá usar a `HubException` classe. Se você lançar um `HubException` do seu método Hub, o signalr **enviará** a mensagem inteira para o cliente, sem modificações.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> O signalr envia apenas `Message` a propriedade da exceção para o cliente. O rastreamento de pilha e outras propriedades na exceção não estão disponíveis para o cliente.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução à ASP.NET Core Signalr](xref:signalr/introduction)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
