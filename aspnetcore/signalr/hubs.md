---
title: Usar hubs no ASP.NET Core SignalR
author: bradygaster
description: Saiba como usar hubs no ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: f95766cab84bddff2c7c62f30bce1e6d1e43deab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963799"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a>Usar hubs no SignalR para ASP.NET Core

Por [Rachel appel](https://twitter.com/rachelappel) e [Kevin Griffin](https://twitter.com/1kevgriff)

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-opno-locsignalr-hub"></a>O que é um hub de SignalR

A API de hubs de SignalR permite que você chame métodos em clientes conectados do servidor. No código do servidor, você define os métodos que são chamados pelo cliente. No código do cliente, você define os métodos que são chamados do servidor. SignalR cuida de tudo nos bastidores que possibilitam a comunicação de cliente para servidor e servidor para cliente em tempo real possível.

## <a name="configure-opno-locsignalr-hubs"></a>Configurar hubs de SignalR

O middleware SignalR requer alguns serviços, que são configurados chamando `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Ao adicionar SignalR funcionalidade a um aplicativo ASP.NET Core, configure SignalR rotas chamando `endpoint.MapHub` no retorno de chamada `Startup.Configure` do método `app.UseEndpoints`.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Ao adicionar SignalR funcionalidade a um aplicativo ASP.NET Core, configure SignalR rotas chamando `app.UseSignalR` no método `Startup.Configure`.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Criar e usar hubs

Crie um hub declarando uma classe que herda de `Hub`e adicione métodos públicos a ela. Os clientes podem chamar métodos que são definidos como `public`.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Você pode especificar um tipo de retorno e parâmetros, incluindo tipos complexos e matrizes, como faria em C# qualquer método. SignalR lida com a serialização e desserialização de objetos e matrizes complexos em seus parâmetros e valores de retorno.

> [!NOTE]
> Os hubs são transitórios:
>
> * Não armazene o estado em uma propriedade na classe Hub. Cada chamada de método de Hub é executada em uma nova instância de Hub.
> * Use `await` ao chamar métodos assíncronos que dependem do Hub permanecendo ativo. Por exemplo, um método como `Clients.All.SendAsync(...)` pode falhar se for chamado sem `await` e o método de Hub for concluído antes de `SendAsync` ser concluído.

## <a name="the-context-object"></a>O objeto de contexto

A classe `Hub` tem uma propriedade `Context` que contém as seguintes propriedades com informações sobre a conexão:

| propriedade | Descrição |
| ------ | ----------- |
| `ConnectionId` | Obtém a ID exclusiva da conexão, atribuída por SignalR. Há uma ID de conexão para cada conexão.|
| `UserIdentifier` | Obtém o [identificador de usuário](xref:signalr/groups). Por padrão, SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado à conexão como o identificador de usuário. |
| `User` | Obtém o `ClaimsPrincipal` associado ao usuário atual. |
| `Items` | Obtém uma coleção de chave/valor que pode ser usada para compartilhar dados dentro do escopo desta conexão. Os dados podem ser armazenados nessa coleção e serão mantidos para a conexão entre invocações de método de Hub diferentes. |
| `Features` | Obtém a coleção de recursos disponíveis na conexão. Por enquanto, essa coleção não é necessária na maioria dos cenários, portanto, ela ainda não está documentada em detalhes. |
| `ConnectionAborted` | Obtém um `CancellationToken` que notifica quando a conexão é anulada. |

o `Hub.Context` também contém os seguintes métodos:

| Método | Descrição |
| ------ | ----------- |
| `GetHttpContext` | Retorna o `HttpContext` para a conexão ou `null` se a conexão não estiver associada a uma solicitação HTTP. Para conexões HTTP, você pode usar esse método para obter informações como cabeçalhos HTTP e cadeias de caracteres de consulta. |
| `Abort` | Anula a conexão. |

## <a name="the-clients-object"></a>O objeto clients

A classe `Hub` tem uma propriedade `Clients` que contém as seguintes propriedades para comunicação entre o servidor e o cliente:

| propriedade | Descrição |
| ------ | ----------- |
| `All` | Chama um método em todos os clientes conectados |
| `Caller` | Chama um método no cliente que invocou o método de Hub |
| `Others` | Chama um método em todos os clientes conectados, exceto o cliente que invocou o método |

o `Hub.Clients` também contém os seguintes métodos:

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

Cada propriedade ou método nas tabelas anteriores retorna um objeto com um método `SendAsync`. O método `SendAsync` permite que você forneça o nome e os parâmetros do método de cliente a ser chamado.

## <a name="send-messages-to-clients"></a>Enviar mensagens para clientes

Para fazer chamadas para clientes específicos, use as propriedades do objeto `Clients`. No exemplo a seguir, há três métodos de Hub:

* `SendMessage` envia uma mensagem para todos os clientes conectados, usando `Clients.All`.
* `SendMessageToCaller` envia uma mensagem de volta ao chamador, usando `Clients.Caller`.
* `SendMessageToGroups` envia uma mensagem para todos os clientes no grupo de `SignalR Users`.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Hubs com rigidez de tipos

Uma desvantagem de usar `SendAsync` é que ela se baseia em uma cadeia de caracteres mágica para especificar o método de cliente a ser chamado. Isso deixa o código aberto para erros de tempo de execução se o nome do método for digitado incorretamente ou ausente no cliente.

Uma alternativa ao uso de `SendAsync` é digitar fortemente o `Hub` com <xref:Microsoft.AspNetCore.SignalR.Hub%601>. No exemplo a seguir, os métodos de cliente `ChatHub` foram extraídos em uma interface chamada `IChatClient`.

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Essa interface pode ser usada para refatorar o exemplo de `ChatHub` anterior.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

O uso de `Hub<IChatClient>` permite a verificação de tempo de compilação dos métodos de cliente. Isso evita problemas causados pelo uso de cadeias de caracteres mágicas, pois `Hub<T>` só pode fornecer acesso aos métodos definidos na interface.

Usar um `Hub<T>` fortemente tipado desabilita a capacidade de usar `SendAsync`. Todos os métodos definidos na interface ainda podem ser definidos como assíncronos. Na verdade, cada um desses métodos deve retornar um `Task`. Como é uma interface, não use a palavra-chave `async`. Por exemplo:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> O sufixo de `Async` não é removido do nome do método. A menos que o método de cliente seja definido com `.on('MyMethodAsync')`, você não deve usar `MyMethodAsync` como um nome.

## <a name="change-the-name-of-a-hub-method"></a>Alterar o nome de um método de Hub

Por padrão, um nome de método de Hub de servidor é o nome do método .NET. No entanto, você pode usar o atributo [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) para alterar esse padrão e especificar manualmente um nome para o método. O cliente deve usar esse nome, em vez do nome do método .NET, ao invocar o método.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Manipular eventos para uma conexão

A API de hubs de SignalR fornece o `OnConnectedAsync` e `OnDisconnectedAsync` métodos virtuais para gerenciar e controlar as conexões. Substitua o método virtual `OnConnectedAsync` para executar ações quando um cliente se conectar ao Hub, como adicioná-lo a um grupo.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Substitua o método virtual `OnDisconnectedAsync` para executar ações quando um cliente se desconectar. Se o cliente se desconectar intencionalmente (chamando `connection.stop()`, por exemplo), o parâmetro `exception` será `null`. No entanto, se o cliente for desconectado devido a um erro (como uma falha de rede), o parâmetro `exception` conterá uma exceção que descreve a falha.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>Tratar erros

As exceções geradas em seus métodos de Hub são enviadas ao cliente que invocou o método. No cliente JavaScript, o método `invoke` retorna uma [promessa de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Quando o cliente recebe um erro com um manipulador anexado à promessa usando `catch`, ele é invocado e passado como um objeto JavaScript `Error`.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Se o Hub lançar uma exceção, as conexões não serão fechadas. Por padrão, SignalR retorna uma mensagem de erro genérica para o cliente. Por exemplo:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Exceções inesperadas geralmente contêm informações confidenciais, como o nome de um servidor de banco de dados em uma exceção disparada quando a conexão de banco de dados falha. o SignalR não expõe essas mensagens de erro detalhadas por padrão como uma medida de segurança. Consulte o [artigo considerações sobre segurança](xref:signalr/security#exceptions) para obter mais informações sobre por que os detalhes da exceção são suprimidos.

Se você tiver uma condição *excepcional que deseja* propagar para o cliente, poderá usar a classe `HubException`. Se você lançar uma `HubException` do seu método de Hub, **SignalR** enviará a mensagem inteira para o cliente, sem modificações.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR envia apenas a propriedade `Message` da exceção para o cliente. O rastreamento de pilha e outras propriedades na exceção não estão disponíveis para o cliente.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução à ASP.NET Core SignalR](xref:signalr/introduction)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
