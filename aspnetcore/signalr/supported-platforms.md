---
title: ASP.NET Core SignalR plataformas com suporte
author: bradygaster
description: Saiba mais sobre as plataformas com suporte para SignalRde ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 86ba5b1aec230d78c1a0e1709187e129df6cb4cc
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963737"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a><span data-ttu-id="0f901-103">ASP.NET Core SignalR plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="0f901-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="0f901-104">Requisitos de sistema do servidor</span><span class="sxs-lookup"><span data-stu-id="0f901-104">Server system requirements</span></span>

SignalR<span data-ttu-id="0f901-105"> para ASP.NET Core dá suporte a qualquer plataforma de servidor com suporte no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f901-105"> for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="0f901-106">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="0f901-106">JavaScript client</span></span>

<span data-ttu-id="0f901-107">O [cliente JavaScript](https://www.npmjs.com/package/@aspnet/signalr) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:</span><span class="sxs-lookup"><span data-stu-id="0f901-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="0f901-108">Navegador</span><span class="sxs-lookup"><span data-stu-id="0f901-108">Browser</span></span>                         | <span data-ttu-id="0f901-109">Version</span><span class="sxs-lookup"><span data-stu-id="0f901-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="0f901-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="0f901-110">Microsoft Edge</span></span>                  | <span data-ttu-id="0f901-111">&dagger; atual</span><span class="sxs-lookup"><span data-stu-id="0f901-111">Current&dagger;</span></span> |
| <span data-ttu-id="0f901-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="0f901-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="0f901-113">&dagger; atual</span><span class="sxs-lookup"><span data-stu-id="0f901-113">Current&dagger;</span></span> |
| <span data-ttu-id="0f901-114">Google Chrome; inclui Android</span><span class="sxs-lookup"><span data-stu-id="0f901-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="0f901-115">&dagger; atual</span><span class="sxs-lookup"><span data-stu-id="0f901-115">Current&dagger;</span></span> |
| <span data-ttu-id="0f901-116">Safari inclui iOS</span><span class="sxs-lookup"><span data-stu-id="0f901-116">Safari; includes iOS</span></span>            | <span data-ttu-id="0f901-117">&dagger; atual</span><span class="sxs-lookup"><span data-stu-id="0f901-117">Current&dagger;</span></span> |
| <span data-ttu-id="0f901-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="0f901-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="0f901-119">11</span><span class="sxs-lookup"><span data-stu-id="0f901-119">11</span></span>              |

<span data-ttu-id="0f901-120">&dagger;*atual* refere-se à versão mais recente do navegador.</span><span class="sxs-lookup"><span data-stu-id="0f901-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="0f901-121">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="0f901-121">.NET client</span></span>

<span data-ttu-id="0f901-122">O [cliente .net](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) é executado em qualquer plataforma com suporte do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f901-122">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="0f901-123">Por exemplo, [os desenvolvedores do xamarin podem usar SignalR](https://github.com/aspnet/Announcements/issues/305) para criar aplicativos Android usando o Xamarin. Android 8.4.0.1 e versões posteriores e aplicativos Ios usando o Xamarin. Ios 11.14.0.4 e posterior.</span><span class="sxs-lookup"><span data-stu-id="0f901-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="0f901-124">Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="0f901-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="0f901-125">Outros transportes têm suporte em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="0f901-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="0f901-126">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="0f901-126">Java client</span></span>

<span data-ttu-id="0f901-127">O [cliente Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) dá suporte a Java 8 e versões posteriores.</span><span class="sxs-lookup"><span data-stu-id="0f901-127">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="0f901-128">Clientes sem suporte</span><span class="sxs-lookup"><span data-stu-id="0f901-128">Unsupported clients</span></span>

<span data-ttu-id="0f901-129">Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais.</span><span class="sxs-lookup"><span data-stu-id="0f901-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="0f901-130">Atualmente, eles não têm suporte e podem nunca ser.</span><span class="sxs-lookup"><span data-stu-id="0f901-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="0f901-131">[C++cliente](https://github.com/aspnet/SignalR/tree/master/clients/cpp)</span><span class="sxs-lookup"><span data-stu-id="0f901-131">[C++ client](https://github.com/aspnet/SignalR/tree/master/clients/cpp)</span></span>

* <span data-ttu-id="0f901-132">[Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="0f901-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
