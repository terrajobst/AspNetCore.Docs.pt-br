---
title: Usando os hubs de SignalR do ASP.NET Core
author: bradygaster
description: Saiba como usar os hubs do SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: f037c1f6fd7ef773b8e7b2fd4fdf6e28222c441a
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327255"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Usar os hubs no SignalR do ASP.NET Core

Por [Rachel Appel](https://twitter.com/rachelappel) e [Kevin Griffin](https://twitter.com/1kevgriff)

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(como fazer o download)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>O que é um hub SignalR

A API de Hubs de SignalR permite chamar métodos em clientes conectados do servidor. No código do servidor, você define métodos que são chamados pelo cliente. O código do cliente, você define métodos que são chamados do servidor. SignalR cuida de tudo o que nos bastidores que possibilita a comunicação de servidor para cliente e servidor-cliente em tempo real.

## <a name="configure-signalr-hubs"></a>Configurar os hubs de SignalR

O middleware SignalR requer alguns serviços, que são configurados por meio da chamada `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

Ao adicionar a funcionalidade do SignalR para um aplicativo ASP.NET Core, configurar as rotas do SignalR chamando `app.UseSignalR` no `Startup.Configure` método.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a>Criar e usar os hubs

Criar um hub, declarando uma classe que herda de `Hub`e adicione os métodos públicos a ele. Os clientes poderão chamar métodos que são definidos como `public`.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Você pode especificar um tipo de retorno e parâmetros, incluindo tipos complexos e matrizes, como você faria em qualquer método em c#. O SignalR lida com a serialização e desserialização de objetos complexos e matrizes em seus valores de retorno e parâmetros.

> [!NOTE]
> Os hubs são transitórios:
>
> * Não armazene o estado em uma propriedade na classe hub. Cada chamada de método de hub é executada em uma nova instância de hub.  
> * Use `await` ao chamar métodos assíncronos que dependem do hub de permanecer ativo. Por exemplo, um método, como `Clients.All.SendAsync(...)` pode falhar se ele for chamado sem `await` e o método de hub seja concluída antes de `SendAsync` for concluída.

## <a name="the-context-object"></a>O objeto de contexto

O `Hub` classe tem um `Context` propriedade que contém as propriedades a seguir com informações sobre a conexão:

| Propriedade | Descrição |
| ------ | ----------- |
| `ConnectionId` | Obtém a ID exclusiva para a conexão, atribuído pelo SignalR. Há uma ID de conexão para cada conexão.|
| `UserIdentifier` | Obtém o [identificador de usuário](xref:signalr/groups). Por padrão, o SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado com a conexão como o identificador de usuário. |
| `User` | Obtém o `ClaimsPrincipal` associado ao usuário atual. |
| `Items` | Obtém uma coleção de chave/valor que pode ser usada para compartilhar dados dentro do escopo dessa conexão. Dados podem ser armazenados nessa coleção e ela será mantida para a conexão entre as invocações de método de hub diferentes. |
| `Features` | Obtém a coleção de recursos disponíveis sobre a conexão. Por enquanto, essa coleção não é necessária na maioria dos cenários, portanto, ele ainda não está documentado em detalhes. |
| `ConnectionAborted` | Obtém um `CancellationToken` que notifica quando a conexão será anulada. |

`Hub.Context` também contém os seguintes métodos:

| Método | Descrição |
| ------ | ----------- |
| `GetHttpContext` | Retorna o `HttpContext` para a conexão, ou `null` se a conexão não está associado uma solicitação HTTP. Para conexões HTTP, você pode usar esse método para obter informações como cadeias de caracteres de consulta e cabeçalhos HTTP. |
| `Abort` | Anula a conexão. |

## <a name="the-clients-object"></a>O objeto de clientes

O `Hub` classe tem um `Clients` propriedade que contém as seguintes propriedades para a comunicação entre cliente e servidor:

| Propriedade | Descrição |
| ------ | ----------- |
| `All` | Chama um método em todos os clientes conectados |
| `Caller` | Chama um método no cliente que invocou o método de hub |
| `Others` | Chama um método em todos os clientes conectados, exceto o cliente que invocou o método |

`Hub.Clients` também contém os seguintes métodos:

| Método | Descrição |
| ------ | ----------- |
| `AllExcept` | Chama um método em todos os clientes conectados, exceto para as conexões especificadas |
| `Client` | Chama um método em um cliente conectado específico |
| `Clients` | Chama um método em clientes conectados específicos |
| `Group` | Chama um método em todas as conexões no grupo especificado  |
| `GroupExcept` | Chama um método em todas as conexões do grupo especificado, exceto as conexões especificadas |
| `Groups` | Chama um método em vários grupos de conexões  |
| `OthersInGroup` | Chama um método em um grupo de conexões, excluindo o cliente que invocou o método de hub  |
| `User` | Chama um método em todas as conexões associadas a um usuário específico |
| `Users` | Chama um método em todas as conexões associadas com os usuários especificados |

Cada propriedade ou método nas tabelas anteriores retorna um objeto com um `SendAsync` método. O `SendAsync` método permite que você forneça o nome e parâmetros do método de cliente para chamar.

## <a name="send-messages-to-clients"></a>Enviar mensagens para os clientes

Para fazer chamadas para clientes específicos, use as propriedades do `Clients` objeto. No exemplo a seguir, há três métodos de Hub:

* `SendMessage` envia uma mensagem para todos os clientes conectados usando `Clients.All`.
* `SendMessageToCaller` envia uma mensagem de volta para o chamador usando `Clients.Caller`.
* `SendMessageToGroups` envia uma mensagem a todos os clientes a `SignalR Users` grupo.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Hubs com rigidez de tipos

Uma desvantagem de usar `SendAsync` é que ele se baseia em uma cadeia de caracteres mágica para especificar o método de cliente a ser chamado. Isso deixa o código aberto para erros de tempo de execução se o nome do método está incorreto ou ausente do cliente.

Uma alternativa ao uso `SendAsync` é tipar fortemente os `Hub` com <xref:Microsoft.AspNetCore.SignalR.Hub%601>. No exemplo a seguir, o `ChatHub` métodos de cliente foram extraídos por em uma interface chamada `IChatClient`.  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Essa interface pode ser usada para refatorar anterior `ChatHub` exemplo.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Usando `Hub<IChatClient>` habilita a verificação de tempo de compilação dos métodos do cliente. Isso evita problemas causados pelo uso de cadeias de caracteres mágicas desde `Hub<T>` só pode fornecer acesso aos métodos definidos na interface.

Usando fortemente tipado `Hub<T>` desabilita a capacidade de usar `SendAsync`. Todos os métodos definidos na interface ainda podem ser definidos como assíncronos. Na verdade, cada um desses métodos deve retornar um `Task`. Uma vez que ele é uma interface, não use o `async` palavra-chave. Por exemplo:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> O `Async` sufixo não é removido do nome do método. A menos que o método de cliente é definido com `.on('MyMethodAsync')`, você não deve usar `MyMethodAsync` como um nome.

## <a name="change-the-name-of-a-hub-method"></a>Alterar o nome de um método de hub

Por padrão, um nome de método de hub do servidor é o nome do método do .NET. No entanto, você pode usar o [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) atributo para alterar esse padrão e especificar manualmente um nome para o método. O cliente deve usar esse nome, em vez do nome de método do .NET, ao chamar o método.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Manipular eventos para uma conexão

A API de Hubs de SignalR fornece o `OnConnectedAsync` e `OnDisconnectedAsync` métodos virtuais para gerenciar e controlar conexões. Substituir o `OnConnectedAsync` método virtual para executar ações quando um cliente se conecta ao Hub, como adicioná-lo a um grupo.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Substituir o `OnDisconnectedAsync` método virtual para executar ações quando um cliente se desconecta. Se o cliente se desconecta intencionalmente (chamando `connection.stop()`, por exemplo), o `exception` parâmetro será `null`. No entanto, se o cliente for desconectado devido a um erro (como uma falha de rede), o `exception` parâmetro conterá uma exceção que descreve a falha.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>Tratar erros

As exceções geradas em seus métodos de hub são enviadas ao cliente que invocou o método. No cliente JavaScript, o `invoke` método retorna um [promessa JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Quando o cliente recebe um erro com um manipulador anexado à promessa usando `catch`, ele tem chamado e passado como um JavaScript `Error` objeto.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Se o seu Hub de lançar uma exceção, as conexões não estão fechadas. Por padrão, o SignalR retorna uma mensagem de erro genérica para o cliente. Por exemplo:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Exceções inesperadas geralmente contêm informações confidenciais, como o nome de um servidor de banco de dados em uma exceção acionada quando a conexão de banco de dados falha. O SignalR não expõe estas mensagens de erro detalhadas por padrão como medida de segurança. Consulte a [artigo de considerações de segurança](xref:signalr/security#exceptions) para obter mais informações sobre por que os detalhes da exceção são suprimidos.

Se você tiver um excepcional de condição você *fazer* deseja propagar para o cliente, você pode usar o `HubException` classe. Se você lançar uma `HubException` de seu método de hub do SignalR **será** enviar a mensagem inteira para o cliente, sem modificações.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> O SignalR envia apenas o `Message` propriedade da exceção para o cliente. O rastreamento de pilha e outras propriedades na exceção não estão disponíveis para o cliente.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução ao SignalR do ASP.NET Core](xref:signalr/introduction)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
