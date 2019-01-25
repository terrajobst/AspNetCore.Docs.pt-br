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
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="dedb9-103">Enviar mensagens de fora de um hub</span><span class="sxs-lookup"><span data-stu-id="dedb9-103">Send messages from outside a hub</span></span>

<span data-ttu-id="dedb9-104">Por [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="dedb9-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="dedb9-105">O hub do SignalR é a abstração central para enviar mensagens para os clientes conectados ao servidor SignalR.</span><span class="sxs-lookup"><span data-stu-id="dedb9-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="dedb9-106">Também é possível enviar mensagens de outros lugares no seu aplicativo usando o `IHubContext` service.</span><span class="sxs-lookup"><span data-stu-id="dedb9-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="dedb9-107">Este artigo explica como acessar um SignalR `IHubContext` para enviar notificações para clientes externos um hub.</span><span class="sxs-lookup"><span data-stu-id="dedb9-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="dedb9-108">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="dedb9-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="dedb9-109">Obtenha uma instância de IHubContext</span><span class="sxs-lookup"><span data-stu-id="dedb9-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="dedb9-110">No SignalR do ASP.NET Core, você pode acessar uma instância de `IHubContext` por meio da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="dedb9-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="dedb9-111">Você pode injetar uma instância do `IHubContext` em um controlador, middleware ou outro serviço de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="dedb9-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="dedb9-112">Use a instância para enviar mensagens para os clientes.</span><span class="sxs-lookup"><span data-stu-id="dedb9-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="dedb9-113">Isso é diferente do ASP.NET 4.x SignalR que usado GlobalHost para fornecer acesso ao `IHubContext`.</span><span class="sxs-lookup"><span data-stu-id="dedb9-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="dedb9-114">O ASP.NET Core tem uma estrutura de injeção de dependência que remove a necessidade de neste singleton global.</span><span class="sxs-lookup"><span data-stu-id="dedb9-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="dedb9-115">Injetar uma instância do IHubContext em um controlador</span><span class="sxs-lookup"><span data-stu-id="dedb9-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="dedb9-116">Você pode injetar uma instância do `IHubContext` em um controlador, adicionando-o para seu construtor:</span><span class="sxs-lookup"><span data-stu-id="dedb9-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="dedb9-117">Agora, com acesso a uma instância de `IHubContext`, você pode chamar métodos de hub, como se estivessem no próprio hub.</span><span class="sxs-lookup"><span data-stu-id="dedb9-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="dedb9-118">Obtenha uma instância de IHubContext no middleware</span><span class="sxs-lookup"><span data-stu-id="dedb9-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="dedb9-119">Acesso a `IHubContext` dentro do pipeline de middleware da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="dedb9-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> <span data-ttu-id="dedb9-120">Quando os métodos de hub são chamados de fora do `Hub` de classe, não há nenhum chamador associado com a invocação.</span><span class="sxs-lookup"><span data-stu-id="dedb9-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="dedb9-121">Portanto, não há nenhum acesso para o `ConnectionId`, `Caller`, e `Others` propriedades.</span><span class="sxs-lookup"><span data-stu-id="dedb9-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="dedb9-122">Injetar um HubContext fortemente tipados</span><span class="sxs-lookup"><span data-stu-id="dedb9-122">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="dedb9-123">Para injetar um HubContext fortemente tipadas, certifique-se de seu Hub herda de `Hub<T>`.</span><span class="sxs-lookup"><span data-stu-id="dedb9-123">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="dedb9-124">Injetá-lo usando o `IHubContext<THub, T>` interface em vez de `IHubContext<THub>`.</span><span class="sxs-lookup"><span data-stu-id="dedb9-124">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

## <a name="related-resources"></a><span data-ttu-id="dedb9-125">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="dedb9-125">Related resources</span></span>

* [<span data-ttu-id="dedb9-126">Introdução</span><span class="sxs-lookup"><span data-stu-id="dedb9-126">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="dedb9-127">Hubs</span><span class="sxs-lookup"><span data-stu-id="dedb9-127">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="dedb9-128">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="dedb9-128">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
