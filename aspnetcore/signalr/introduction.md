---
title: Introdução ao SignalR do ASP.NET Core
author: bradygaster
description: Saiba como a biblioteca SignalR do ASP.NET Core simplifica a adição de funcionalidade em tempo real aos aplicativos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836357"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="6a02f-103">Introdução ao SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a02f-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="6a02f-104">O que é o SignalR?</span><span class="sxs-lookup"><span data-stu-id="6a02f-104">What is SignalR?</span></span>

<span data-ttu-id="6a02f-105">SignalR do ASP.NET Core é uma biblioteca de software livre que simplifica a adição da funcionalidade da web em tempo real aos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6a02f-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="6a02f-106">A funcionalidade da web em tempo real permite que o código do lado do servidor para conteúdo de envio por push aos clientes instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="6a02f-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="6a02f-107">Bons candidatos para o SignalR:</span><span class="sxs-lookup"><span data-stu-id="6a02f-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="6a02f-108">Aplicativos que exigem atualizações de alta frequência do servidor.</span><span class="sxs-lookup"><span data-stu-id="6a02f-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="6a02f-109">Exemplos são jogos, redes sociais, de votação, leilão, mapas e aplicativos GPS.</span><span class="sxs-lookup"><span data-stu-id="6a02f-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="6a02f-110">Os painéis e monitoramento de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6a02f-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="6a02f-111">Exemplos incluem painéis da empresa, atualizações de vendas instantâneas, ou alertas de viagem.</span><span class="sxs-lookup"><span data-stu-id="6a02f-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="6a02f-112">Aplicativos de colaboração.</span><span class="sxs-lookup"><span data-stu-id="6a02f-112">Collaborative apps.</span></span> <span data-ttu-id="6a02f-113">Quadro de comunicações aplicativos e software de reunião de equipe são exemplos de aplicativos de colaboração.</span><span class="sxs-lookup"><span data-stu-id="6a02f-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="6a02f-114">Aplicativos que exigem as notificações.</span><span class="sxs-lookup"><span data-stu-id="6a02f-114">Apps that require notifications.</span></span> <span data-ttu-id="6a02f-115">Redes sociais, email, bate-papo, jogos, alertas de viagem e muitos outros aplicativos usam notificações.</span><span class="sxs-lookup"><span data-stu-id="6a02f-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="6a02f-116">O SignalR fornece uma API para a criação de servidor para cliente [chamadas de procedimento remoto (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span><span class="sxs-lookup"><span data-stu-id="6a02f-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="6a02f-117">As RPCs chamam funções JavaScript em clientes do código do lado do servidor do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a02f-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="6a02f-118">Aqui estão alguns recursos do SignalR para ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6a02f-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="6a02f-119">Lida com gerenciamento de conexão automaticamente.</span><span class="sxs-lookup"><span data-stu-id="6a02f-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="6a02f-120">Envia mensagens para todos os clientes conectados simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="6a02f-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="6a02f-121">Por exemplo, uma sala de bate-papo.</span><span class="sxs-lookup"><span data-stu-id="6a02f-121">For example, a chat room.</span></span>
* <span data-ttu-id="6a02f-122">Envia mensagens para clientes específicos ou grupos de clientes.</span><span class="sxs-lookup"><span data-stu-id="6a02f-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="6a02f-123">É dimensionada para lidar com o aumento de tráfego.</span><span class="sxs-lookup"><span data-stu-id="6a02f-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="6a02f-124">A fonte é hospedada em um [SignalR repositório no GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="6a02f-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="6a02f-125">Transportes</span><span class="sxs-lookup"><span data-stu-id="6a02f-125">Transports</span></span>

<span data-ttu-id="6a02f-126">O SignalR dá suporte a várias técnicas para manipular as comunicações em tempo real:</span><span class="sxs-lookup"><span data-stu-id="6a02f-126">SignalR supports several techniques for handling real-time communications:</span></span>

* [<span data-ttu-id="6a02f-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="6a02f-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="6a02f-128">Eventos enviados pelo servidor</span><span class="sxs-lookup"><span data-stu-id="6a02f-128">Server-Sent Events</span></span>
* <span data-ttu-id="6a02f-129">Sondagem longa</span><span class="sxs-lookup"><span data-stu-id="6a02f-129">Long Polling</span></span>

<span data-ttu-id="6a02f-130">O SignalR escolhe automaticamente o melhor método de transporte que está dentro de recursos do servidor e cliente.</span><span class="sxs-lookup"><span data-stu-id="6a02f-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="6a02f-131">Hubs</span><span class="sxs-lookup"><span data-stu-id="6a02f-131">Hubs</span></span>

<span data-ttu-id="6a02f-132">Usa o SignalR *hubs* para comunicação entre clientes e servidores.</span><span class="sxs-lookup"><span data-stu-id="6a02f-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="6a02f-133">Um hub é um pipeline de alto nível que permite que um cliente e servidor chamar métodos em si.</span><span class="sxs-lookup"><span data-stu-id="6a02f-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="6a02f-134">O SignalR manipula a expedição entre limites de máquina automaticamente, permitindo que os clientes chamar métodos no servidor e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="6a02f-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="6a02f-135">Você pode passar parâmetros fortemente tipados para métodos, que habilita a associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="6a02f-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="6a02f-136">O SignalR fornece dois protocolos de hub interno: um protocolo de texto com base em JSON e um protocolo binário, com base em [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="6a02f-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="6a02f-137">MessagePack geralmente cria mensagens menores em comparação comparadas JSON.</span><span class="sxs-lookup"><span data-stu-id="6a02f-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="6a02f-138">Devem dar suporte a navegadores mais antigos [nível XHR 2](https://caniuse.com/#feat=xhr2) para fornecer suporte de protocolo MessagePack.</span><span class="sxs-lookup"><span data-stu-id="6a02f-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="6a02f-139">Os hubs de chamar o código do lado do cliente enviando mensagens que contêm o nome e parâmetros do método do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="6a02f-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="6a02f-140">Os objetos enviados como parâmetros de método são desserializados usando o protocolo configurado.</span><span class="sxs-lookup"><span data-stu-id="6a02f-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="6a02f-141">O cliente tenta corresponder o nome a um método no código do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="6a02f-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="6a02f-142">Quando o cliente encontra uma correspondência, ele chama o método e passa a ele os dados de parâmetro desserializado.</span><span class="sxs-lookup"><span data-stu-id="6a02f-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a02f-143">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6a02f-143">Additional resources</span></span>

* [<span data-ttu-id="6a02f-144">Introdução ao SignalR para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a02f-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6a02f-145">Plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="6a02f-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="6a02f-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="6a02f-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6a02f-147">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="6a02f-147">JavaScript client</span></span>](xref:signalr/javascript-client)
