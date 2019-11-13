---
title: Introdução ao ASP.NET Core SignalR
author: bradygaster
description: Saiba como o ASP.NET Core SignalR library simplifica a adição de funcionalidades em tempo real aos aplicativos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 7108d9f223db78937dd1203a1cb4b890006b20ec
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963946"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a><span data-ttu-id="09fe7-103">Introdução ao ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="09fe7-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-opno-locsignalr"></a><span data-ttu-id="09fe7-104">O que é SignalR?</span><span class="sxs-lookup"><span data-stu-id="09fe7-104">What is SignalR?</span></span>

<span data-ttu-id="09fe7-105">ASP.NET Core SignalR é uma biblioteca de software livre que simplifica a adição da funcionalidade da Web em tempo real a aplicativos.</span><span class="sxs-lookup"><span data-stu-id="09fe7-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="09fe7-106">A funcionalidade da Web em tempo real permite que o código do lado do servidor envie conteúdo aos clientes instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="09fe7-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="09fe7-107">Bons candidatos para SignalR:</span><span class="sxs-lookup"><span data-stu-id="09fe7-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="09fe7-108">Aplicativos que exigem atualizações de alta frequência do servidor.</span><span class="sxs-lookup"><span data-stu-id="09fe7-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="09fe7-109">Exemplos são jogos, redes sociais, votação, leilão, mapas e aplicativos de GPS.</span><span class="sxs-lookup"><span data-stu-id="09fe7-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="09fe7-110">Painéis e aplicativos de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="09fe7-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="09fe7-111">Os exemplos incluem painéis da empresa, atualizações de vendas instantâneas ou alertas de viagem.</span><span class="sxs-lookup"><span data-stu-id="09fe7-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="09fe7-112">Aplicativos de colaboração.</span><span class="sxs-lookup"><span data-stu-id="09fe7-112">Collaborative apps.</span></span> <span data-ttu-id="09fe7-113">Os aplicativos de quadro de comunicações e o software de reunião de equipe são exemplos de aplicativos de colaboração.</span><span class="sxs-lookup"><span data-stu-id="09fe7-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="09fe7-114">Aplicativos que exigem notificações.</span><span class="sxs-lookup"><span data-stu-id="09fe7-114">Apps that require notifications.</span></span> <span data-ttu-id="09fe7-115">Redes sociais, email, bate-papo, jogos, alertas de viagem e muitos outros aplicativos usam notificações.</span><span class="sxs-lookup"><span data-stu-id="09fe7-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="09fe7-116"> fornece uma API para criar [chamadas de procedimento remoto (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)de servidor para cliente.</span><span class="sxs-lookup"><span data-stu-id="09fe7-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="09fe7-117">As RPCs chamam funções JavaScript em clientes do código .NET Core do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="09fe7-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="09fe7-118">Aqui estão alguns recursos de SignalR para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="09fe7-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="09fe7-119">Lida com o gerenciamento de conexão automaticamente.</span><span class="sxs-lookup"><span data-stu-id="09fe7-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="09fe7-120">Envia mensagens a todos os clientes conectados simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="09fe7-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="09fe7-121">Por exemplo, uma sala de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="09fe7-121">For example, a chat room.</span></span>
* <span data-ttu-id="09fe7-122">Envia mensagens para clientes ou grupos de clientes específicos.</span><span class="sxs-lookup"><span data-stu-id="09fe7-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="09fe7-123">Dimensiona para lidar com o aumento do tráfego.</span><span class="sxs-lookup"><span data-stu-id="09fe7-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="09fe7-124">A origem é hospedada em um [repositórioSignalR no GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="09fe7-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="09fe7-125">Transportes</span><span class="sxs-lookup"><span data-stu-id="09fe7-125">Transports</span></span>

<span data-ttu-id="09fe7-126">o SignalR dá suporte a várias técnicas para lidar com comunicações em tempo real:</span><span class="sxs-lookup"><span data-stu-id="09fe7-126">SignalR supports several techniques for handling real-time communications:</span></span>

* [<span data-ttu-id="09fe7-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="09fe7-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="09fe7-128">Eventos enviados pelo servidor</span><span class="sxs-lookup"><span data-stu-id="09fe7-128">Server-Sent Events</span></span>
* <span data-ttu-id="09fe7-129">Sondagem longa</span><span class="sxs-lookup"><span data-stu-id="09fe7-129">Long Polling</span></span>

SignalR<span data-ttu-id="09fe7-130"> escolhe automaticamente o melhor método de transporte que está dentro dos recursos do servidor e do cliente.</span><span class="sxs-lookup"><span data-stu-id="09fe7-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="09fe7-131">Hubs</span><span class="sxs-lookup"><span data-stu-id="09fe7-131">Hubs</span></span>

<span data-ttu-id="09fe7-132">o SignalR usa *hubs* para se comunicar entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="09fe7-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="09fe7-133">Um hub é um pipeline de alto nível que permite que um cliente e um servidor chamem métodos entre si.</span><span class="sxs-lookup"><span data-stu-id="09fe7-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="09fe7-134">o SignalR manipula a expedição entre limites de máquina automaticamente, permitindo que os clientes chamem métodos no servidor e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="09fe7-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="09fe7-135">Você pode passar parâmetros fortemente tipados para métodos, o que habilita a associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="09fe7-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="09fe7-136">o SignalR fornece dois protocolos de Hub internos: um protocolo de texto baseado em JSON e um protocolo binário com base em [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="09fe7-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="09fe7-137">O MessagePack geralmente cria mensagens menores em comparação com o JSON.</span><span class="sxs-lookup"><span data-stu-id="09fe7-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="09fe7-138">Os navegadores mais antigos devem dar suporte ao [nível 2 do XHR](https://caniuse.com/#feat=xhr2) para fornecer suporte ao protocolo MessagePack.</span><span class="sxs-lookup"><span data-stu-id="09fe7-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="09fe7-139">Os hubs chamam o código do lado do cliente enviando mensagens que contêm o nome e os parâmetros do método do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="09fe7-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="09fe7-140">Os objetos enviados como parâmetros de método são desserializados usando o protocolo configurado.</span><span class="sxs-lookup"><span data-stu-id="09fe7-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="09fe7-141">O cliente tenta corresponder o nome a um método no código do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="09fe7-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="09fe7-142">Quando o cliente encontra uma correspondência, ele chama o método e passa para ele os dados de parâmetro desserializados.</span><span class="sxs-lookup"><span data-stu-id="09fe7-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09fe7-143">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="09fe7-143">Additional resources</span></span>

* <span data-ttu-id="09fe7-144">[Introdução ao SignalR para ASP.NET Core](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="09fe7-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="09fe7-145">Plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="09fe7-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="09fe7-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="09fe7-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="09fe7-147">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="09fe7-147">JavaScript client</span></span>](xref:signalr/javascript-client)
