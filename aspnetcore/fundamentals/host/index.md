---
title: Host da Web e Host Genérico no ASP.NET Core
author: guardrex
description: Saiba mais sobre o Host da Web do ASP.NET Core e o Host Genérico do .NET, que são responsáveis pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/28/2018
uid: fundamentals/host/index
ms.openlocfilehash: 43a68f67368ce37a1fb4032579c6c4e4c05d2719
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284572"
---
# <a name="web-host-and-generic-host-in-aspnet-core"></a><span data-ttu-id="d08eb-103">Host da Web e Host Genérico no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d08eb-103">Web Host and Generic Host in ASP.NET Core</span></span>

<span data-ttu-id="d08eb-104">Aplicativos ASP.NET Core configuram e inicializam um *host*.</span><span class="sxs-lookup"><span data-stu-id="d08eb-104">.NET apps configure and launch a *host*.</span></span> <span data-ttu-id="d08eb-105">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d08eb-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d08eb-106">Duas APIs de host estão disponíveis para uso:</span><span class="sxs-lookup"><span data-stu-id="d08eb-106">Two host APIs are available for use:</span></span>

* <span data-ttu-id="d08eb-107">[Host da Web](xref:fundamentals/host/web-host) &ndash; Adequado para a hospedagem de aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="d08eb-107">[Web Host](xref:fundamentals/host/web-host) &ndash; Suitable for hosting web apps.</span></span>
* <span data-ttu-id="d08eb-108">[Host Genérico](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 ou posteriores) &ndash; Adequado para a hospedagem de aplicativos não Web (por exemplo, aplicativos que executam tarefas em segundo plano).</span><span class="sxs-lookup"><span data-stu-id="d08eb-108">[Generic Host](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 or later) &ndash; Suitable for hosting non-web apps (for example, apps that run background tasks).</span></span> <span data-ttu-id="d08eb-109">Em uma versão futura, o Host Genérico será adequado para hospedar qualquer tipo de aplicativo, incluindo aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="d08eb-109">In a future release, the Generic Host will be suitable for hosting any kind of app, including web apps.</span></span> <span data-ttu-id="d08eb-110">Eventualmente, o Host Genérico substituirá o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="d08eb-110">The Generic Host will eventually replace the Web Host.</span></span>

<span data-ttu-id="d08eb-111">Para hospedar *aplicativos Web* do ASP.NET Core, os desenvolvedores devem usar o Web Host com base em <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d08eb-111">For hosting ASP.NET Core *web apps*, developers should use the Web Host based on <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>.</span></span> <span data-ttu-id="d08eb-112">Para hospedar *aplicativos que não sejam Web*, os desenvolvedores devem usar o Host Genérico com base em <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d08eb-112">For hosting *non-web apps*, developers should use the Generic Host based on <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

<xref:fundamentals/host/hosted-services>  
<span data-ttu-id="d08eb-113">Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d08eb-113">Learn how to implement background tasks with hosted services in ASP.NET Core.</span></span>

<xref:fundamentals/configuration/platform-specific-configuration>  
<span data-ttu-id="d08eb-114">Descubra como aprimorar um aplicativo ASP.NET Core por meio de um assembly referenciado ou não referenciado usando uma implementação de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>.</span><span class="sxs-lookup"><span data-stu-id="d08eb-114">Discover how to enhance an ASP.NET Core app from a referenced or unreferenced assembly using an <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation.</span></span>
