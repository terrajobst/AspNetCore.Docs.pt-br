---
title: Recursos de cliente SignalR
author: bradygaster
description: Saiba quais recursos são compatíveis com os vários clientes do Signalr ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301183"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="be22a-103">ASP.NET Core recursos de cliente do Signalr</span><span class="sxs-lookup"><span data-stu-id="be22a-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="be22a-104">Distribuição de recursos</span><span class="sxs-lookup"><span data-stu-id="be22a-104">Feature distribution</span></span>

<span data-ttu-id="be22a-105">A tabela a seguir mostra os recursos e o suporte para os clientes que oferecem suporte em tempo real.</span><span class="sxs-lookup"><span data-stu-id="be22a-105">The table below shows the features and support for the clients that offer real-time support.</span></span>

| <span data-ttu-id="be22a-106">Recurso</span><span class="sxs-lookup"><span data-stu-id="be22a-106">Feature</span></span> | <span data-ttu-id="be22a-107">.NET</span><span class="sxs-lookup"><span data-stu-id="be22a-107">.NET</span></span> | <span data-ttu-id="be22a-108">JavaScript</span><span class="sxs-lookup"><span data-stu-id="be22a-108">JavaScript</span></span> | <span data-ttu-id="be22a-109">Java</span><span class="sxs-lookup"><span data-stu-id="be22a-109">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="be22a-110">Suporte ao serviço de Signaler do Azure</span><span class="sxs-lookup"><span data-stu-id="be22a-110">Azure SignalR Service Support</span></span> |<span data-ttu-id="be22a-111">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-111">✔</span></span>|<span data-ttu-id="be22a-112">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-112">✔</span></span>|<span data-ttu-id="be22a-113">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-113">✔</span></span>|
| [<span data-ttu-id="be22a-114">Streaming de servidor para cliente</span><span class="sxs-lookup"><span data-stu-id="be22a-114">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="be22a-115">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-115">✔</span></span>|<span data-ttu-id="be22a-116">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-116">✔</span></span>|<span data-ttu-id="be22a-117">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-117">✔</span></span>|
| [<span data-ttu-id="be22a-118">Streaming de cliente para servidor</span><span class="sxs-lookup"><span data-stu-id="be22a-118">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="be22a-119">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-119">✔</span></span>|<span data-ttu-id="be22a-120">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-120">✔</span></span>|<span data-ttu-id="be22a-121">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-121">✔</span></span>|
| <span data-ttu-id="be22a-122">Reconexão automática ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span><span class="sxs-lookup"><span data-stu-id="be22a-122">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="be22a-123">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-123">✔</span></span>|<span data-ttu-id="be22a-124">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-124">✔</span></span>| |
| <span data-ttu-id="be22a-125">Transporte de WebSockets</span><span class="sxs-lookup"><span data-stu-id="be22a-125">WebSockets Transport</span></span> |<span data-ttu-id="be22a-126">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-126">✔</span></span>|<span data-ttu-id="be22a-127">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-127">✔</span></span>|<span data-ttu-id="be22a-128">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-128">✔</span></span>|
| <span data-ttu-id="be22a-129">Transporte de eventos enviados pelo servidor</span><span class="sxs-lookup"><span data-stu-id="be22a-129">Server-Sent Events Transport</span></span> |<span data-ttu-id="be22a-130">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-130">✔</span></span>|<span data-ttu-id="be22a-131">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-131">✔</span></span>| |
| <span data-ttu-id="be22a-132">Transporte de sondagem longa</span><span class="sxs-lookup"><span data-stu-id="be22a-132">Long Polling Transport</span></span> |<span data-ttu-id="be22a-133">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-133">✔</span></span>|<span data-ttu-id="be22a-134">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-134">✔</span></span>|<span data-ttu-id="be22a-135">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-135">✔</span></span>|
| <span data-ttu-id="be22a-136">Protocolo de Hub JSON</span><span class="sxs-lookup"><span data-stu-id="be22a-136">JSON Hub Protocol</span></span> |<span data-ttu-id="be22a-137">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-137">✔</span></span>|<span data-ttu-id="be22a-138">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-138">✔</span></span>|<span data-ttu-id="be22a-139">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-139">✔</span></span>|
| <span data-ttu-id="be22a-140">Protocolo de Hub do MessagePack</span><span class="sxs-lookup"><span data-stu-id="be22a-140">MessagePack Hub Protocol</span></span> |<span data-ttu-id="be22a-141">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-141">✔</span></span>|<span data-ttu-id="be22a-142">✔</span><span class="sxs-lookup"><span data-stu-id="be22a-142">✔</span></span>| |

<span data-ttu-id="be22a-143">O suporte para reconexão automática no cliente Java é acompanhado em [nosso Issue Tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span><span class="sxs-lookup"><span data-stu-id="be22a-143">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be22a-144">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="be22a-144">Additional resources</span></span>

* [<span data-ttu-id="be22a-145">Introdução ao Signalr para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be22a-145">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="be22a-146">Plataformas compatíveis</span><span class="sxs-lookup"><span data-stu-id="be22a-146">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="be22a-147">Hubs</span><span class="sxs-lookup"><span data-stu-id="be22a-147">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="be22a-148">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="be22a-148">JavaScript client</span></span>](xref:signalr/javascript-client)
