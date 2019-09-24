---
title: Hospedar e implantar ASP.NET Core servidor mais novo
author: guardrex
description: Saiba como hospedar e implantar um aplicativo de servidor mais novo usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: aedef7fe695dd4a0cbf04d3f3e9947f33f7afa40
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211611"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="5ae5d-103">Hospedar e implantar um servidor mais novo</span><span class="sxs-lookup"><span data-stu-id="5ae5d-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="5ae5d-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5ae5d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="5ae5d-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="5ae5d-105">Host configuration values</span></span>

<span data-ttu-id="5ae5d-106">Os [aplicativos de servidor mais incrivelmente](xref:blazor/hosting-models#blazor-server) podem aceitar valores de configuração de [host genéricos](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="5ae5d-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="5ae5d-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="5ae5d-107">Deployment</span></span>

<span data-ttu-id="5ae5d-108">Usando o [modelo de Hospedagem de servidor mais incrivelmente](xref:blazor/hosting-models#blazor-server), o mais incrivelmente é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="5ae5d-109">As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="5ae5d-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="5ae5d-110">É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="5ae5d-111">O Visual Studio inclui o modelo de projeto **Aplicativo de Servidor Blazor** (modelo `blazorserverside` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="5ae5d-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="5ae5d-112">Dimensionamento</span><span class="sxs-lookup"><span data-stu-id="5ae5d-112">Scalability</span></span>

<span data-ttu-id="5ae5d-113">Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um aplicativo de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="5ae5d-114">Consulte os seguintes recursos para abordar a escalabilidade do aplicativo de servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="5ae5d-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="5ae5d-115">Conceitos básicos dos aplicativos de servidor mais importantes</span><span class="sxs-lookup"><span data-stu-id="5ae5d-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="5ae5d-116">Servidor de implantação</span><span class="sxs-lookup"><span data-stu-id="5ae5d-116">Deployment server</span></span>

<span data-ttu-id="5ae5d-117">Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="5ae5d-118">A memória disponível no servidor afeta o:</span><span class="sxs-lookup"><span data-stu-id="5ae5d-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="5ae5d-119">Número de circuitos ativos aos quais um servidor pode dar suporte.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="5ae5d-120">Latência da interface do usuário no cliente.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-120">UI latency on the client.</span></span>

<span data-ttu-id="5ae5d-121">Para obter orientação sobre a criação de aplicativos de servidor mais seguros e <xref:security/blazor/server>escalonáveis, consulte.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="5ae5d-122">Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="5ae5d-123">O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="5ae5d-124">Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: Se você espera que seu aplicativo dê suporte a 5.000 usuários simultâneos, considere o orçamento de pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).</span><span class="sxs-lookup"><span data-stu-id="5ae5d-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="5ae5d-125">Configuração do SignalR</span><span class="sxs-lookup"><span data-stu-id="5ae5d-125">SignalR configuration</span></span>

<span data-ttu-id="5ae5d-126">Os aplicativos de servidor mais incrivelmente usam ASP.NET Core Signalr para se comunicar com o navegador.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="5ae5d-127">As [condições de hospedagem e de colocação do signalr](xref:signalr/publish-to-azure-web-app) aplicam-se a aplicativos de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="5ae5d-128">O mais bem funciona melhor ao usar o WebSocket como o transporte do Signalr devido à menor latência, confiabilidade e [segurança](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="5ae5d-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="5ae5d-129">A sondagem longa é usada pelo Signalr quando o WebSockets não está disponível ou quando o aplicativo é configurado explicitamente para usar a sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="5ae5d-130">Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="5ae5d-131">Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [diretrizes de publicação do signalr](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="5ae5d-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

<span data-ttu-id="5ae5d-132">É recomendável usar o [serviço de signalr do Azure](/azure/azure-signalr) para aplicativos de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-132">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="5ae5d-133">O serviço permite escalar verticalmente um aplicativo de servidor mais recente para um grande número de conexões de Signaler simultâneas.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="5ae5d-134">Além disso, o alcance global do serviço Signalr e os data centers de alto desempenho ajudam significativamente na redução da latência devido à geografia.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="5ae5d-135">Medir latência de rede</span><span class="sxs-lookup"><span data-stu-id="5ae5d-135">Measure network latency</span></span>

<span data-ttu-id="5ae5d-136">A [interoperabilidade js](xref:blazor/javascript-interop) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5ae5d-136">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="5ae5d-137">Para uma experiência de interface do usuário razoável, recomendamos uma latência de interface do usuário sustentada de 250 MS ou menos.</span><span class="sxs-lookup"><span data-stu-id="5ae5d-137">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
