---
title: Hospedar e implantar o Blazor no servidor
author: guardrex
description: Saiba como hospedar e implantar um aplicativo Blazor do servidor usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 940020ee44d72d50395aad64bc924413c1bbecfb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614618"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="91fe8-103">Hospedar e implantar o Blazor no servidor</span><span class="sxs-lookup"><span data-stu-id="91fe8-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="91fe8-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="91fe8-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="91fe8-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="91fe8-105">Host configuration values</span></span>

<span data-ttu-id="91fe8-106">Os aplicativos do servidor que usam o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side-hosting-model) podem aceitar [valores de configuração do Host Genérico](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="91fe8-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side-hosting-model) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="91fe8-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="91fe8-107">Deployment</span></span>

<span data-ttu-id="91fe8-108">Com o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side-hosting-model), o Blazor é executado no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91fe8-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side-hosting-model), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="91fe8-109">As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="91fe8-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="91fe8-110">O aplicativo é incluído com o aplicativo do ASP.NET Core no resultado publicado e os dois aplicativos são implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="91fe8-110">The app is included with the ASP.NET Core app in the published output, and the two apps are deployed together.</span></span> <span data-ttu-id="91fe8-111">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91fe8-111">A web server that's capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="91fe8-112">Para uma implantação do lado do servidor, o Visual Studio inclui o modelo de projeto **Componentes do Razor** (modelo `razorcomponents` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="91fe8-112">For a server-side deployment, Visual Studio includes the **Razor Components** project template (`razorcomponents` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="91fe8-113">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="91fe8-113">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="91fe8-114">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="91fe8-114">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>
