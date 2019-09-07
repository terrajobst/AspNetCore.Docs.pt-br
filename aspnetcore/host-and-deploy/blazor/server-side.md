---
title: Hospedar e implantar o ASP.NET Core Blazor no lado do servidor
author: guardrex
description: Saiba como hospedar e implantar um aplicativo Blazor do servidor usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8da71faf6abc5929d6cd43d42fd896e378d99ef6
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773581"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="63ac4-103">Hospedar e implantar o Blazor no servidor</span><span class="sxs-lookup"><span data-stu-id="63ac4-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="63ac4-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="63ac4-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="63ac4-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="63ac4-105">Host configuration values</span></span>

<span data-ttu-id="63ac4-106">Os aplicativos do servidor que usam o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side) podem aceitar [valores de configuração do Host Genérico](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="63ac4-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="63ac4-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="63ac4-107">Deployment</span></span>

<span data-ttu-id="63ac4-108">Com o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side), o Blazor é executado no servidor em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63ac4-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="63ac4-109">As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="63ac4-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="63ac4-110">É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63ac4-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="63ac4-111">O Visual Studio inclui o modelo de projeto **Aplicativo de Servidor Blazor** (modelo `blazorserverside` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="63ac4-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="connection-scale-out"></a><span data-ttu-id="63ac4-112">Expandir a conexão</span><span class="sxs-lookup"><span data-stu-id="63ac4-112">Connection scale out</span></span>

<span data-ttu-id="63ac4-113">Os aplicativos Blazor do lado do servidor exigem uma conexão ativa do SignalR para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="63ac4-113">Blazor server-side apps require one active SignalR connection for each user.</span></span> <span data-ttu-id="63ac4-114">Uma implantação do lado do servidor de produção do Blazor requer uma solução para dar suporte a várias conexões simultâneas solicitadas pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="63ac4-114">A production Blazor server-side deployment requires a solution for supporting as many concurrent connections as required by the app.</span></span> <span data-ttu-id="63ac4-115">O [Serviço do Azure SignalR](/azure/azure-signalr/) lida com o dimensionamento de conexões e é recomendado como uma solução de dimensionamento para aplicativos Blazor do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="63ac4-115">The [Azure SignalR Service](/azure/azure-signalr/) handles the scaling of connections and is recommended as a scaling solution for Blazor server-side apps.</span></span> <span data-ttu-id="63ac4-116">Para obter mais informações, consulte <xref:signalr/publish-to-azure-web-app>.</span><span class="sxs-lookup"><span data-stu-id="63ac4-116">For more information, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="signalr-configuration"></a><span data-ttu-id="63ac4-117">Configuração do SignalR</span><span class="sxs-lookup"><span data-stu-id="63ac4-117">SignalR configuration</span></span>

<span data-ttu-id="63ac4-118">O SignalR é configurado pelo ASP.NET Core para os cenários Blazor mais comuns do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="63ac4-118">SignalR is configured by ASP.NET Core for the most common Blazor server-side scenarios.</span></span> <span data-ttu-id="63ac4-119">Para cenários avançados e personalizados, confira os artigos de SignalR na seção [Recursos adicionais](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="63ac4-119">For custom and advanced scenarios, consult the SignalR articles in the [Additional resources](#additional-resources) section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63ac4-120">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="63ac4-120">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="63ac4-121">Documentação do Serviço do Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="63ac4-121">Azure SignalR Service Documentation</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="63ac4-122">Início Rápido: Criar uma sala de chat usando o Serviço do SignalR</span><span class="sxs-lookup"><span data-stu-id="63ac4-122">Quickstart: Create a chat room by using SignalR Service</span></span>](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="63ac4-123">Implantar a versão de visualização do ASP.NET Core ao Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="63ac4-123">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
