---
title: SignalR de ASP.NET Core do host em serviços em segundo plano
author: bradygaster
description: Saiba como enviar mensagens para clientes SignalR de classes BackgroundService do .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 000732115153eeafed3948c2a07acf77ffc34218
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73964049"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a><span data-ttu-id="89770-103">SignalR de ASP.NET Core do host em serviços em segundo plano</span><span class="sxs-lookup"><span data-stu-id="89770-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="89770-104">Por [Brady GASTER](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="89770-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="89770-105">Este artigo fornece diretrizes para:</span><span class="sxs-lookup"><span data-stu-id="89770-105">This article provides guidance for:</span></span>

* <span data-ttu-id="89770-106">Hospedagem de hubs de SignalR usando um processo de trabalho em segundo plano hospedado com ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89770-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="89770-107">Envio de mensagens para clientes conectados de dentro de um [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="89770-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="89770-108">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="89770-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-opno-locsignalr-during-startup"></a><span data-ttu-id="89770-109">Conectar SignalR durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="89770-109">Wire up SignalR during startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89770-110">Hospedar ASP.NET Core hubs de SignalR no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89770-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="89770-111">No método `Startup.ConfigureServices`, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência ASP.NET Core (DI) para dar suporte ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="89770-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="89770-112">No `Startup.Configure`, o método `MapHub` é chamado no retorno de chamada `UseEndpoints` para conectar os pontos de extremidade do Hub no pipeline de solicitação ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89770-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="89770-113">Hospedar ASP.NET Core hubs de SignalR no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89770-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="89770-114">No método `Startup.ConfigureServices`, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência ASP.NET Core (DI) para dar suporte ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="89770-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="89770-115">No `Startup.Configure`, o método `UseSignalR` é chamado para conectar os pontos de extremidade do Hub no pipeline de solicitação ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89770-115">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="89770-116">No exemplo anterior, a classe `ClockHub` implementa a classe `Hub<T>` para criar um hub fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="89770-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="89770-117">A `ClockHub` foi configurada na classe `Startup` para responder às solicitações no ponto de extremidade `/hubs/clock`.</span><span class="sxs-lookup"><span data-stu-id="89770-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="89770-118">Para obter mais informações sobre hubs com rigidez de tipos, consulte [usar hubs em SignalR para ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="89770-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="89770-119">Essa funcionalidade não é limitada ao [Hub\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1) classe.</span><span class="sxs-lookup"><span data-stu-id="89770-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="89770-120">Qualquer classe que herda do [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), também funcionará.</span><span class="sxs-lookup"><span data-stu-id="89770-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="89770-121">A interface usada pelo `ClockHub` com rigidez de tipos é a interface `IClock`.</span><span class="sxs-lookup"><span data-stu-id="89770-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="89770-122">Chamar um hub de SignalR de um serviço em segundo plano</span><span class="sxs-lookup"><span data-stu-id="89770-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="89770-123">Durante a inicialização, a classe `Worker`, uma `BackgroundService`, é conectada usando `AddHostedService`.</span><span class="sxs-lookup"><span data-stu-id="89770-123">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="89770-124">Como SignalR também é conectado durante a fase de `Startup`, na qual cada Hub é anexado a um ponto de extremidade individual no pipeline de solicitação HTTP de ASP.NET Core, cada Hub é representado por um `IHubContext<T>` no servidor.</span><span class="sxs-lookup"><span data-stu-id="89770-124">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="89770-125">Usando os recursos de DI do ASP.NET Core, outras classes instanciadas pela camada de hospedagem, como classes de `BackgroundService`, classes do controlador MVC ou modelos de página do Razor, podem obter referências a hubs do lado do servidor aceitando instâncias de `IHubContext<ClockHub, IClock>` durante a construção.</span><span class="sxs-lookup"><span data-stu-id="89770-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="89770-126">Como o método `ExecuteAsync` é chamado iterativamente no serviço em segundo plano, a data e hora atuais do servidor são enviadas para os clientes conectados usando o `ClockHub`.</span><span class="sxs-lookup"><span data-stu-id="89770-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-opno-locsignalr-events-with-background-services"></a><span data-ttu-id="89770-127">Reagir a SignalR eventos com serviços em segundo plano</span><span class="sxs-lookup"><span data-stu-id="89770-127">React to SignalR events with background services</span></span>

<span data-ttu-id="89770-128">Como um aplicativo de página única usando o cliente JavaScript para SignalR ou um aplicativo de área de trabalho .NET pode usar o <xref:signalr/dotnet-client>, uma implementação `BackgroundService` ou `IHostedService` também pode ser usada para se conectar a hubs de SignalR e responder a eventos.</span><span class="sxs-lookup"><span data-stu-id="89770-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="89770-129">A classe `ClockHubClient` implementa a interface `IClock` e a interface `IHostedService`.</span><span class="sxs-lookup"><span data-stu-id="89770-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="89770-130">Dessa forma, ele pode ser conectado durante a `Startup` para ser executado continuamente e responder a eventos de Hub do servidor.</span><span class="sxs-lookup"><span data-stu-id="89770-130">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="89770-131">Durante a inicialização, o `ClockHubClient` cria uma instância de um `HubConnection` e conecta o método `IClock.ShowTime` como o manipulador para o evento de `ShowTime` do Hub.</span><span class="sxs-lookup"><span data-stu-id="89770-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="89770-132">Na implementação de `IHostedService.StartAsync`, o `HubConnection` é iniciado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="89770-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="89770-133">Durante o método `IHostedService.StopAsync`, a `HubConnection` é descartada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="89770-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="89770-134">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="89770-134">Additional resources</span></span>

* [<span data-ttu-id="89770-135">Introdução</span><span class="sxs-lookup"><span data-stu-id="89770-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="89770-136">Hubs</span><span class="sxs-lookup"><span data-stu-id="89770-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="89770-137">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="89770-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="89770-138">Hubs com rigidez de tipos</span><span class="sxs-lookup"><span data-stu-id="89770-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
