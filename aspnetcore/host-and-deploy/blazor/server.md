---
title: Hospedar e implantar ASP.NET Core Blazor Server
author: guardrex
description: Saiba como hospedar e implantar um aplicativo do Blazor Server usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: e8b3a7faaf1dc88059a79abbc7e74657ebb2068c
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726735"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="d53ec-103">Hospedar e implantar o servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="d53ec-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="d53ec-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d53ec-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="d53ec-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="d53ec-105">Host configuration values</span></span>

<span data-ttu-id="d53ec-106">os [aplicativos doBlazor Server](xref:blazor/hosting-models#blazor-server) podem aceitar [valores de configuração de host genéricos](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="d53ec-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="d53ec-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="d53ec-107">Deployment</span></span>

<span data-ttu-id="d53ec-108">Usando o [modelo de hospedagem do servidorBlazor](xref:blazor/hosting-models#blazor-server), Blazor é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d53ec-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="d53ec-109">As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma conexão [SignalR](xref:signalr/introduction) .</span><span class="sxs-lookup"><span data-stu-id="d53ec-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="d53ec-110">É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d53ec-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="d53ec-111">O Visual Studio inclui o modelo de projeto de **aplicativo doBlazor Server** (`blazorserverside` modelo ao usar o comando [dotnet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="d53ec-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="d53ec-112">Escalabilidade</span><span class="sxs-lookup"><span data-stu-id="d53ec-112">Scalability</span></span>

<span data-ttu-id="d53ec-113">Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d53ec-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="d53ec-114">Consulte os seguintes recursos para resolver a escalabilidade do aplicativo Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="d53ec-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="d53ec-115">[Conceitos básicos de aplicativos do Blazor Server](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="d53ec-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="d53ec-116">Servidor de implantação</span><span class="sxs-lookup"><span data-stu-id="d53ec-116">Deployment server</span></span>

<span data-ttu-id="d53ec-117">Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem.</span><span class="sxs-lookup"><span data-stu-id="d53ec-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="d53ec-118">A memória disponível no servidor afeta o:</span><span class="sxs-lookup"><span data-stu-id="d53ec-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="d53ec-119">Número de circuitos ativos aos quais um servidor pode dar suporte.</span><span class="sxs-lookup"><span data-stu-id="d53ec-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="d53ec-120">Latência da interface do usuário no cliente.</span><span class="sxs-lookup"><span data-stu-id="d53ec-120">UI latency on the client.</span></span>

<span data-ttu-id="d53ec-121">Para obter orientação sobre a criação de aplicativos de servidor Blazor de segurança e escalonáveis, consulte <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="d53ec-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="d53ec-122">Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*.</span><span class="sxs-lookup"><span data-stu-id="d53ec-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="d53ec-123">O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente.</span><span class="sxs-lookup"><span data-stu-id="d53ec-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="d53ec-124">Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: se você espera que seu aplicativo ofereça suporte a 5.000 usuários simultâneos, considere o orçamento para menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).</span><span class="sxs-lookup"><span data-stu-id="d53ec-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a><span data-ttu-id="d53ec-125">configuração de SignalR</span><span class="sxs-lookup"><span data-stu-id="d53ec-125">SignalR configuration</span></span>

<span data-ttu-id="d53ec-126">os aplicativos do Blazor Server usam ASP.NET Core SignalR para se comunicar com o navegador.</span><span class="sxs-lookup"><span data-stu-id="d53ec-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="d53ec-127">as [condições de hospedagem e colocação deSignalR](xref:signalr/publish-to-azure-web-app) se aplicam aos aplicativos Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d53ec-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="d53ec-128"> funciona melhor ao usar Websockets como transporte de SignalR devido a latência, confiabilidade e [segurança](xref:signalr/security)menores.</span><span class="sxs-lookup"><span data-stu-id="d53ec-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="d53ec-129">A sondagem longa é usada pelo SignalR quando o WebSockets não está disponível ou quando o aplicativo é configurado explicitamente para usar a sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="d53ec-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="d53ec-130">Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço.</span><span class="sxs-lookup"><span data-stu-id="d53ec-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="d53ec-131">Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [diretrizes de publicação deSignalR](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="d53ec-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="d53ec-132">Serviço de SignalR do Azure</span><span class="sxs-lookup"><span data-stu-id="d53ec-132">Azure SignalR Service</span></span>

<span data-ttu-id="d53ec-133">É recomendável usar o [serviço de SignalR do Azure](/azure/azure-signalr) para aplicativos do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d53ec-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="d53ec-134">O serviço permite escalar verticalmente um aplicativo do Blazor Server para um grande número de conexões de SignalR simultâneas.</span><span class="sxs-lookup"><span data-stu-id="d53ec-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="d53ec-135">Além disso, o alcance global do serviço de SignalR e os data centers de alto desempenho auxiliam significativamente na redução da latência devido à geografia.</span><span class="sxs-lookup"><span data-stu-id="d53ec-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="d53ec-136">Para configurar um aplicativo (e, opcionalmente, provisionar) o serviço de SignalR do Azure:</span><span class="sxs-lookup"><span data-stu-id="d53ec-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="d53ec-137">Habilite o serviço para dar suporte a *sessões adesivas*, em que os clientes são [redirecionados de volta para o mesmo servidor ao renderizar](xref:blazor/hosting-models#reconnection-to-the-same-server).</span><span class="sxs-lookup"><span data-stu-id="d53ec-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#reconnection-to-the-same-server).</span></span> <span data-ttu-id="d53ec-138">Defina a opção `ServerStickyMode` ou o valor de configuração como `Required`.</span><span class="sxs-lookup"><span data-stu-id="d53ec-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="d53ec-139">Normalmente, um aplicativo cria a configuração usando **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d53ec-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="d53ec-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d53ec-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="d53ec-141">Configuração (use **uma** das seguintes abordagens):</span><span class="sxs-lookup"><span data-stu-id="d53ec-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="d53ec-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d53ec-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="d53ec-143">A **configuração** do serviço de aplicativo > **configurações do aplicativo** na portal do Azure (**nome**: `Azure:SignalR:ServerStickyMode`, **valor**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="d53ec-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="d53ec-144">Crie um perfil de publicação de aplicativos do Azure no Visual Studio para o aplicativo Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="d53ec-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="d53ec-145">Adicione a dependência do **serviço de SignalR do Azure** ao perfil.</span><span class="sxs-lookup"><span data-stu-id="d53ec-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="d53ec-146">Se a assinatura do Azure não tiver uma instância de serviço de SignalR do Azure já existente para atribuir ao aplicativo, selecione **criar uma nova instância do serviço de SignalR do Azure** para provisionar uma nova instância de serviço.</span><span class="sxs-lookup"><span data-stu-id="d53ec-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="d53ec-147">Publicar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="d53ec-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="d53ec-148">IIS</span><span class="sxs-lookup"><span data-stu-id="d53ec-148">IIS</span></span>

<span data-ttu-id="d53ec-149">Ao usar o IIS, as sessões adesivas são habilitadas com Application Request Routing.</span><span class="sxs-lookup"><span data-stu-id="d53ec-149">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="d53ec-150">Para obter mais informações, consulte [balanceamento de carga http usando Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="d53ec-150">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="d53ec-151">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="d53ec-151">Kubernetes</span></span>

<span data-ttu-id="d53ec-152">Crie uma definição de entrada com as seguintes [anotações de kubernetes para sessões adesivas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="d53ec-152">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="d53ec-153">Linux com o Nginx</span><span class="sxs-lookup"><span data-stu-id="d53ec-153">Linux with Nginx</span></span>

<span data-ttu-id="d53ec-154">Para que SignalR WebSockets funcionem corretamente, defina os cabeçalhos `Upgrade` e `Connection` do proxy como o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d53ec-154">For SignalR WebSockets to function properly, set the proxy's `Upgrade` and `Connection` headers to the following:</span></span>

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="d53ec-155">Para obter mais informações, consulte [Nginx como um proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="d53ec-155">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="d53ec-156">Medir latência de rede</span><span class="sxs-lookup"><span data-stu-id="d53ec-156">Measure network latency</span></span>

<span data-ttu-id="d53ec-157">A [interoperabilidade js](xref:blazor/javascript-interop) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d53ec-157">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
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

<span data-ttu-id="d53ec-158">Para uma experiência de interface do usuário razoável, recomendamos uma latência de interface do usuário sustentada de 250 MS ou menos.</span><span class="sxs-lookup"><span data-stu-id="d53ec-158">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
