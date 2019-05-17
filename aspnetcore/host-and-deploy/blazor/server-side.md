---
title: Hospedar e implantar o Blazor no servidor
author: guardrex
description: Saiba como hospedar e implantar um aplicativo Blazor do servidor usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/26/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8e44be09a4cceba2509f3e86abf3ce5fd2d077bd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887761"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="79e21-103">Hospedar e implantar o Blazor no servidor</span><span class="sxs-lookup"><span data-stu-id="79e21-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="79e21-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="79e21-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="79e21-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="79e21-105">Host configuration values</span></span>

<span data-ttu-id="79e21-106">Os aplicativos do servidor que usam o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side) podem aceitar [valores de configuração do Host Genérico](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="79e21-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="79e21-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="79e21-107">Deployment</span></span>

<span data-ttu-id="79e21-108">Com o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side), o Blazor é executado no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="79e21-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="79e21-109">As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="79e21-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="79e21-110">É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="79e21-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="79e21-111">O Visual Studio inclui o modelo de projeto **Blazor (lado do servidor)** (modelo `blazorserverside` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="79e21-111">Visual Studio includes the **Blazor (server-side)** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a><span data-ttu-id="79e21-112">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="79e21-112">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="79e21-113">Implantar a versão de visualização do ASP.NET Core ao Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="79e21-113">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
