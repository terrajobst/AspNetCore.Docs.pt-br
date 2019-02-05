---
title: Host SignalR do ASP.NET Core em serviços em segundo plano
author: bradygaster
description: Saiba como enviar mensagens para os clientes do SignalR de classes do .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: b359bd7f6b0667aeb8d9c8f5eb450637b1347b19
ms.sourcegitcommit: e418cb9cddeb3de06fa0cb4fdb5529da03ff6d63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55739664"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="5cb27-103">Host SignalR do ASP.NET Core em serviços em segundo plano</span><span class="sxs-lookup"><span data-stu-id="5cb27-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="5cb27-104">Por [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="5cb27-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="5cb27-105">Este artigo fornece orientações para:</span><span class="sxs-lookup"><span data-stu-id="5cb27-105">This article provides guidance for:</span></span>

* <span data-ttu-id="5cb27-106">Hospedagem de Hubs de SignalR usando um processo de trabalho em segundo plano hospedado com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5cb27-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="5cb27-107">Enviando mensagens para clientes de dentro de um núcleo do .NET de conectados [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span><span class="sxs-lookup"><span data-stu-id="5cb27-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="5cb27-108">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5cb27-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="5cb27-109">Conectar o SignalR durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="5cb27-109">Wire up SignalR during startup</span></span>

<span data-ttu-id="5cb27-110">Os Hubs de SignalR do ASP.NET Core no contexto de um processo de trabalho em segundo plano de hospedagem é idêntico à hospedagem de um Hub em um aplicativo web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5cb27-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="5cb27-111">No `Startup.ConfigureServices` chamar um método, `services.AddSignalR` adiciona os serviços necessários para a camada de injeção de dependência de núcleo do ASP.NET (DI) para dar suporte ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="5cb27-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="5cb27-112">Na `Startup.Configure`, o `UseSignalR` método é chamado para ligar os pontos de extremidade de Hub no pipeline de solicitação do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5cb27-112">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

<span data-ttu-id="5cb27-113">No exemplo anterior, o `ClockHub` classe implementa o `Hub<T>` classe para criar um Hub com rigidez de tipos.</span><span class="sxs-lookup"><span data-stu-id="5cb27-113">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="5cb27-114">O `ClockHub` tiver sido configurado na `Startup` classe responder às solicitações no ponto de extremidade `/hubs/clock`.</span><span class="sxs-lookup"><span data-stu-id="5cb27-114">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="5cb27-115">Para obter mais informações sobre os Hubs com rigidez de tipos, consulte [usando os hubs de SignalR do ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="5cb27-115">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="5cb27-116">Essa funcionalidade não está limitada para o [Hub\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) classe.</span><span class="sxs-lookup"><span data-stu-id="5cb27-116">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="5cb27-117">Qualquer classe que herda de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), também funcionará.</span><span class="sxs-lookup"><span data-stu-id="5cb27-117">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="5cb27-118">A interface usada pelo fortemente tipados `ClockHub` é o `IClock` interface.</span><span class="sxs-lookup"><span data-stu-id="5cb27-118">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="5cb27-119">Chamar um SignalR Hub de um serviço em segundo plano</span><span class="sxs-lookup"><span data-stu-id="5cb27-119">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="5cb27-120">Durante a inicialização, o `Worker` classe, uma `BackgroundService`, está conectada usando `AddHostedService`.</span><span class="sxs-lookup"><span data-stu-id="5cb27-120">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="5cb27-121">Uma vez que o SignalR também está conectado durante o `Startup` fase, em que cada Hub está anexado a um ponto de extremidade individual no pipeline de solicitação HTTP do ASP.NET Core, cada Hub é representado por um `IHubContext<T>` no servidor.</span><span class="sxs-lookup"><span data-stu-id="5cb27-121">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="5cb27-122">Usando o ASP.NET Core injeção de dependência recursos, outras classes instanciadas por meio da camada de hospedagem, como `BackgroundService` classes, classes de controlador MVC ou modelos de página do Razor, podem obter referências para os Hubs do lado do servidor, aceitando as instâncias de `IHubContext<ClockHub, IClock>` durante a construção.</span><span class="sxs-lookup"><span data-stu-id="5cb27-122">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="5cb27-123">Como o `ExecuteAsync` método é chamado de forma iterativa no serviço do plano de fundo, o servidor data e hora atuais são enviadas para os clientes conectados usando o `ClockHub`.</span><span class="sxs-lookup"><span data-stu-id="5cb27-123">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="5cb27-124">Reagir a eventos do SignalR com serviços em segundo plano</span><span class="sxs-lookup"><span data-stu-id="5cb27-124">React to SignalR events with background services</span></span>

<span data-ttu-id="5cb27-125">Como um aplicativo de página única usando o cliente JavaScript para um aplicativo de área de trabalho do .NET ou SignalR pode fazer usando o usando o <xref:signalr/dotnet-client>, um `BackgroundService` ou `IHostedService` implementação também pode ser usada para conectar-se aos Hubs de SignalR e responder a eventos.</span><span class="sxs-lookup"><span data-stu-id="5cb27-125">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="5cb27-126">O `ClockHubClient` classe implementa ambos o `IClock` interface e o `IHostedService` interface.</span><span class="sxs-lookup"><span data-stu-id="5cb27-126">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="5cb27-127">Dessa forma, ele pode ser conectado durante `Startup` para executar continuamente e responder a eventos do Hub do servidor.</span><span class="sxs-lookup"><span data-stu-id="5cb27-127">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span> 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="5cb27-128">Durante a inicialização, o `ClockHubClient` cria uma instância de um `HubConnection` e conecta os `IClock.ShowTime` método como o manipulador para o Hub `ShowTime` eventos.</span><span class="sxs-lookup"><span data-stu-id="5cb27-128">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="5cb27-129">No `IHostedService.StartAsync` implementação, o `HubConnection` é iniciado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="5cb27-129">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="5cb27-130">Durante o `IHostedService.StopAsync` método, o `HubConnection` é descartado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="5cb27-130">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="5cb27-131">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5cb27-131">Additional resources</span></span>

* [<span data-ttu-id="5cb27-132">Introdução</span><span class="sxs-lookup"><span data-stu-id="5cb27-132">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5cb27-133">Hubs</span><span class="sxs-lookup"><span data-stu-id="5cb27-133">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5cb27-134">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="5cb27-134">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="5cb27-135">Hubs com rigidez de tipos</span><span class="sxs-lookup"><span data-stu-id="5cb27-135">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
