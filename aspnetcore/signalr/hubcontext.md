---
title: O SignalR HubContext
author: bradygaster
description: Saiba como usar o serviço HubContext de SignalR do ASP.NET Core para enviar notificações para clientes externos um hub.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 73cf2c9d30ed5e409a75827fdab1f22b20427884
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837930"
---
# <a name="send-messages-from-outside-a-hub"></a>Enviar mensagens de fora de um hub

Por [Mikael Mengistu](https://twitter.com/MikaelM_12)

O hub do SignalR é a abstração central para enviar mensagens para os clientes conectados ao servidor SignalR. Também é possível enviar mensagens de outros lugares no seu aplicativo usando o `IHubContext` service. Este artigo explica como acessar um SignalR `IHubContext` para enviar notificações para clientes externos um hub.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Obtenha uma instância de IHubContext

No SignalR do ASP.NET Core, você pode acessar uma instância de `IHubContext` por meio da injeção de dependência. Você pode injetar uma instância do `IHubContext` em um controlador, middleware ou outro serviço de injeção de dependência. Use a instância para enviar mensagens para os clientes.

> [!NOTE]
> Isso é diferente do ASP.NET 4.x SignalR que usado GlobalHost para fornecer acesso ao `IHubContext`. O ASP.NET Core tem uma estrutura de injeção de dependência que remove a necessidade de neste singleton global.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Injetar uma instância do IHubContext em um controlador

Você pode injetar uma instância do `IHubContext` em um controlador, adicionando-o para seu construtor:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Agora, com acesso a uma instância de `IHubContext`, você pode chamar métodos de hub, como se estivessem no próprio hub.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Obtenha uma instância de IHubContext no middleware

Acesso a `IHubContext` dentro do pipeline de middleware da seguinte forma:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> Quando os métodos de hub são chamados de fora do `Hub` de classe, não há nenhum chamador associado com a invocação. Portanto, não há nenhum acesso para o `ConnectionId`, `Caller`, e `Others` propriedades.

### <a name="inject-a-strongly-typed-hubcontext"></a>Injetar um HubContext fortemente tipados

Para injetar um HubContext fortemente tipadas, certifique-se de seu Hub herda de `Hub<T>`. Injetá-lo usando o `IHubContext<THub, T>` interface em vez de `IHubContext<THub>`.

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
