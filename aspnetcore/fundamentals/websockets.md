---
title: Suporte ao WebSockets no ASP.NET Core
author: rick-anderson
description: Saiba como começar a usar o WebSockets no ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/17/2019
uid: fundamentals/websockets
ms.openlocfilehash: 76acb9c96ed5e8bbbaf39eeb6cb23307bb44fb8d
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836852"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="4a680-103">Suporte ao WebSockets no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a680-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="4a680-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="4a680-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="4a680-105">Este artigo explica como começar a usar o WebSockets no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4a680-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="4a680-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) é um protocolo que permite canais de comunicação persistentes bidirecionais em conexões TCP.</span><span class="sxs-lookup"><span data-stu-id="4a680-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="4a680-107">Ele é usado em aplicativos que se beneficiam de comunicação rápida e em tempo real, como chat, painel e aplicativos de jogos.</span><span class="sxs-lookup"><span data-stu-id="4a680-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="4a680-108">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4a680-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="4a680-109">Confira a seção [Próximas etapas](#next-steps) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="4a680-109">See the [Next steps](#next-steps) section for more information.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4a680-110">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4a680-110">Prerequisites</span></span>

* <span data-ttu-id="4a680-111">ASP.NET Core 1.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="4a680-111">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="4a680-112">Qualquer sistema operacional compatível com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4a680-112">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="4a680-113">Windows 7/Windows Server 2008 ou posterior</span><span class="sxs-lookup"><span data-stu-id="4a680-113">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="4a680-114">Linux</span><span class="sxs-lookup"><span data-stu-id="4a680-114">Linux</span></span>
  * <span data-ttu-id="4a680-115">macOS</span><span class="sxs-lookup"><span data-stu-id="4a680-115">macOS</span></span>
  
* <span data-ttu-id="4a680-116">Se o aplicativo é executado no Windows com o IIS:</span><span class="sxs-lookup"><span data-stu-id="4a680-116">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="4a680-117">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="4a680-117">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="4a680-118">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="4a680-118">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="4a680-119">WebSockets precisam ser habilitados (confira a seção [Suporte para IIS/IIS Express](#iisiis-express-support)).</span><span class="sxs-lookup"><span data-stu-id="4a680-119">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="4a680-120">Se o aplicativo é executado no [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="4a680-120">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="4a680-121">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="4a680-121">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="4a680-122">Para saber quais são os navegadores compatíveis, confira https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="4a680-122">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="when-to-use-websockets"></a><span data-ttu-id="4a680-123">Quando usar WebSockets</span><span class="sxs-lookup"><span data-stu-id="4a680-123">When to use WebSockets</span></span>

<span data-ttu-id="4a680-124">Use WebSockets para trabalhar diretamente com uma conexão de soquete.</span><span class="sxs-lookup"><span data-stu-id="4a680-124">Use WebSockets to work directly with a socket connection.</span></span> <span data-ttu-id="4a680-125">Por exemplo, use WebSockets para obter o melhor desempenho possível em um jogo em tempo real.</span><span class="sxs-lookup"><span data-stu-id="4a680-125">For example, use WebSockets for the best possible performance with a real-time game.</span></span>

<span data-ttu-id="4a680-126">[SignalR do ASP.NET Core](xref:signalr/introduction) é uma biblioteca que simplifica a adição da funcionalidade da Web em tempo real aos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="4a680-126">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="4a680-127">Ele usa WebSockets sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="4a680-127">It uses WebSockets whenever possible.</span></span>

## <a name="how-to-use-websockets"></a><span data-ttu-id="4a680-128">Como usar WebSockets</span><span class="sxs-lookup"><span data-stu-id="4a680-128">How to use WebSockets</span></span>

* <span data-ttu-id="4a680-129">Instale o pacote [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).</span><span class="sxs-lookup"><span data-stu-id="4a680-129">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>
* <span data-ttu-id="4a680-130">Configure o middleware.</span><span class="sxs-lookup"><span data-stu-id="4a680-130">Configure the middleware.</span></span>
* <span data-ttu-id="4a680-131">Aceite solicitações do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4a680-131">Accept WebSocket requests.</span></span>
* <span data-ttu-id="4a680-132">Envie e receba mensagens.</span><span class="sxs-lookup"><span data-stu-id="4a680-132">Send and receive messages.</span></span>

### <a name="configure-the-middleware"></a><span data-ttu-id="4a680-133">Configurar o middleware</span><span class="sxs-lookup"><span data-stu-id="4a680-133">Configure the middleware</span></span>

<span data-ttu-id="4a680-134">Adicione o middleware do WebSockets no método `Configure` da classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="4a680-134">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4a680-135">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="4a680-135">The following settings can be configured:</span></span>

* <span data-ttu-id="4a680-136">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="4a680-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="4a680-137">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="4a680-137">The default is two minutes.</span></span>
* <span data-ttu-id="4a680-138">`ReceiveBufferSize` – o tamanho do buffer usado para receber dados.</span><span class="sxs-lookup"><span data-stu-id="4a680-138">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="4a680-139">Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados.</span><span class="sxs-lookup"><span data-stu-id="4a680-139">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="4a680-140">O padrão é 4 KB.</span><span class="sxs-lookup"><span data-stu-id="4a680-140">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="4a680-141">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="4a680-141">The following settings can be configured:</span></span>

* <span data-ttu-id="4a680-142">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="4a680-142">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="4a680-143">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="4a680-143">The default is two minutes.</span></span>
* <span data-ttu-id="4a680-144">`ReceiveBufferSize` – o tamanho do buffer usado para receber dados.</span><span class="sxs-lookup"><span data-stu-id="4a680-144">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="4a680-145">Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados.</span><span class="sxs-lookup"><span data-stu-id="4a680-145">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="4a680-146">O padrão é 4 KB.</span><span class="sxs-lookup"><span data-stu-id="4a680-146">The default is 4 KB.</span></span>
* <span data-ttu-id="4a680-147">`AllowedOrigins` – Uma lista de valores de cabeçalho de origem permitidos para solicitações do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4a680-147">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="4a680-148">Por padrão, todas as origens são permitidas.</span><span class="sxs-lookup"><span data-stu-id="4a680-148">By default, all origins are allowed.</span></span> <span data-ttu-id="4a680-149">Consulte "Restrição de origem do WebSocket" abaixo para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="4a680-149">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

### <a name="accept-websocket-requests"></a><span data-ttu-id="4a680-150">Aceitar solicitações do WebSocket</span><span class="sxs-lookup"><span data-stu-id="4a680-150">Accept WebSocket requests</span></span>

<span data-ttu-id="4a680-151">Em algum lugar e em um momento futuro no ciclo de vida da solicitação (posteriormente no método `Configure` ou em uma ação do MVC, por exemplo), verifique se é uma solicitação do WebSocket e aceite-a.</span><span class="sxs-lookup"><span data-stu-id="4a680-151">Somewhere later in the request life cycle (later in the `Configure` method or in an MVC action, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="4a680-152">O exemplo a seguir é de uma fase posterior do método `Configure`:</span><span class="sxs-lookup"><span data-stu-id="4a680-152">The following example is from later in the `Configure` method:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

<span data-ttu-id="4a680-153">Uma solicitação do WebSocket pode entrar em qualquer URL, mas esse código de exemplo aceita apenas solicitações de `/ws`.</span><span class="sxs-lookup"><span data-stu-id="4a680-153">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

### <a name="send-and-receive-messages"></a><span data-ttu-id="4a680-154">Enviar e receber mensagens</span><span class="sxs-lookup"><span data-stu-id="4a680-154">Send and receive messages</span></span>

<span data-ttu-id="4a680-155">O método `AcceptWebSocketAsync` atualiza a conexão TCP para uma conexão WebSocket e fornece um objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="4a680-155">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="4a680-156">Use o objeto `WebSocket` para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="4a680-156">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="4a680-157">O código mostrado anteriormente que aceita a solicitação do WebSocket passa o objeto `WebSocket` para um método `Echo`.</span><span class="sxs-lookup"><span data-stu-id="4a680-157">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="4a680-158">O código recebe uma mensagem e envia de volta imediatamente a mesma mensagem.</span><span class="sxs-lookup"><span data-stu-id="4a680-158">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="4a680-159">As mensagens são enviadas e recebidas em um loop até que o cliente feche a conexão:</span><span class="sxs-lookup"><span data-stu-id="4a680-159">Messages are sent and received in a loop until the client closes the connection:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

<span data-ttu-id="4a680-160">Ao aceitar a conexão WebSocket antes de iniciar o loop, o pipeline de middleware é encerrado.</span><span class="sxs-lookup"><span data-stu-id="4a680-160">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="4a680-161">Ao fechar o soquete, o pipeline é desenrolado.</span><span class="sxs-lookup"><span data-stu-id="4a680-161">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="4a680-162">Ou seja, a solicitação deixa de avançar no pipeline quando o WebSocket é aceito.</span><span class="sxs-lookup"><span data-stu-id="4a680-162">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="4a680-163">Quando o loop é concluído e o soquete é fechado, a solicitação continua a avançar no pipeline.</span><span class="sxs-lookup"><span data-stu-id="4a680-163">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="handle-client-disconnects"></a><span data-ttu-id="4a680-164">Tratar desconexões de cliente</span><span class="sxs-lookup"><span data-stu-id="4a680-164">Handle client disconnects</span></span>

<span data-ttu-id="4a680-165">O servidor não é informado automaticamente quando o cliente se desconecta devido à perda de conectividade.</span><span class="sxs-lookup"><span data-stu-id="4a680-165">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="4a680-166">O servidor recebe uma mensagem de desconexão apenas quando o cliente a envia, e isso é possível somente quando há conexão com a Internet.</span><span class="sxs-lookup"><span data-stu-id="4a680-166">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="4a680-167">Se quiser tomar alguma medida quando isso ocorrer, defina um tempo limite, caso não receba nada do cliente após uma determinada janela de tempo.</span><span class="sxs-lookup"><span data-stu-id="4a680-167">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="4a680-168">Se o cliente não envia mensagens com frequência, e você prefere não definir um tempo limite apenas porque a conexão fica ociosa, peça ao cliente para usar um temporizador a fim de enviar uma mensagem de ping a cada X segundos.</span><span class="sxs-lookup"><span data-stu-id="4a680-168">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="4a680-169">No servidor, se uma mensagem não chegar dentro de 2\*X segundos após a mensagem anterior, encerre a conexão e informe que o cliente se desconectou.</span><span class="sxs-lookup"><span data-stu-id="4a680-169">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="4a680-170">Aguarde o dobro do intervalo de tempo esperado a fim de deixar um tempo extra para atrasos na rede, que possam atrasar a mensagem de ping.</span><span class="sxs-lookup"><span data-stu-id="4a680-170">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

### <a name="websocket-origin-restriction"></a><span data-ttu-id="4a680-171">Restrição de origem do WebSocket</span><span class="sxs-lookup"><span data-stu-id="4a680-171">WebSocket origin restriction</span></span>

<span data-ttu-id="4a680-172">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4a680-172">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="4a680-173">Navegadores **não**:</span><span class="sxs-lookup"><span data-stu-id="4a680-173">Browsers do **not**:</span></span>

* <span data-ttu-id="4a680-174">Executam solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="4a680-174">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="4a680-175">Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4a680-175">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="4a680-176">No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4a680-176">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="4a680-177">Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.</span><span class="sxs-lookup"><span data-stu-id="4a680-177">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="4a680-178">Se você estiver hospedando o servidor em "https://server.com" e hospedando seu cliente em "https://client.com", adicione "https://client.com" à lista `AllowedOrigins` para o WebSockets verificar.</span><span class="sxs-lookup"><span data-stu-id="4a680-178">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="4a680-179">O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado.</span><span class="sxs-lookup"><span data-stu-id="4a680-179">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="4a680-180">**Não** use esses cabeçalhos como um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="4a680-180">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="4a680-181">Suporte ao IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="4a680-181">IIS/IIS Express support</span></span>

<span data-ttu-id="4a680-182">O Windows Server 2012 ou posterior e o Windows 8 ou posterior com o IIS/IIS Express 8 ou posterior são compatíveis com o protocolo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4a680-182">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="4a680-183">WebSockets estão sempre habilitados ao usar o IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4a680-183">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="4a680-184">Habilitar WebSockets no IIS</span><span class="sxs-lookup"><span data-stu-id="4a680-184">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="4a680-185">Para habilitar o suporte para o protocolo WebSocket no Windows Server 2012 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="4a680-185">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="4a680-186">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="4a680-186">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="4a680-187">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.</span><span class="sxs-lookup"><span data-stu-id="4a680-187">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="4a680-188">Selecione **Instalação baseada em função ou em recurso**.</span><span class="sxs-lookup"><span data-stu-id="4a680-188">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="4a680-189">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4a680-189">Select **Next**.</span></span>
1. <span data-ttu-id="4a680-190">Selecione o servidor apropriado (o servidor local é selecionado por padrão).</span><span class="sxs-lookup"><span data-stu-id="4a680-190">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="4a680-191">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4a680-191">Select **Next**.</span></span>
1. <span data-ttu-id="4a680-192">Expanda **Servidor Web (IIS)** na árvore **Funções**, expanda **Servidor Web** e, em seguida, expanda **Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="4a680-192">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="4a680-193">Selecione o **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="4a680-193">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="4a680-194">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4a680-194">Select **Next**.</span></span>
1. <span data-ttu-id="4a680-195">Se não forem necessários recursos adicionais, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4a680-195">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="4a680-196">Clique em **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="4a680-196">Select **Install**.</span></span>
1. <span data-ttu-id="4a680-197">Quando a instalação for concluída, selecione **Fechar** para sair do assistente.</span><span class="sxs-lookup"><span data-stu-id="4a680-197">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="4a680-198">Para habilitar o suporte para o protocolo WebSocket no Windows 8 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="4a680-198">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="4a680-199">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="4a680-199">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="4a680-200">Navegue para **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="4a680-200">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="4a680-201">Abra os seguintes nós: **Serviços de Informações da Internet** > **Serviços da World Wide Web** > **Recursos de Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="4a680-201">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="4a680-202">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="4a680-202">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="4a680-203">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="4a680-203">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="4a680-204">Desabilite o WebSocket ao usar o socket.io no Node.js</span><span class="sxs-lookup"><span data-stu-id="4a680-204">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="4a680-205">Se você estiver usando o suporte do WebSocket no [socket.io](https://socket.io/) no [Node.js](https://nodejs.org/), desabilite o módulo do WebSocket do IIS padrão usando o elemento `webSocket` em *web.config* ou em *applicationHost.config*. Se essa etapa não for executada, o módulo do WebSocket do IIS tentará manipular a comunicação do WebSocket em vez do Node.js e o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4a680-205">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="next-steps"></a><span data-ttu-id="4a680-206">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="4a680-206">Next steps</span></span>

<span data-ttu-id="4a680-207">O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompanha este artigo é um aplicativo de eco.</span><span class="sxs-lookup"><span data-stu-id="4a680-207">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="4a680-208">Ele tem uma página da Web que faz conexões WebSocket e o servidor reenvia para o cliente todas as mensagens recebidas.</span><span class="sxs-lookup"><span data-stu-id="4a680-208">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="4a680-209">Execute o aplicativo em um prompt de comando (ele não está configurado para execução no Visual Studio com o IIS Express) e navegue para http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="4a680-209">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="4a680-210">A página da Web exibe o status de conexão no canto superior esquerdo:</span><span class="sxs-lookup"><span data-stu-id="4a680-210">The web page shows the connection status in the upper left:</span></span>

![Estado inicial da página da Web](websockets/_static/start.png)

<span data-ttu-id="4a680-212">Selecione **Conectar** para enviar uma solicitação WebSocket para a URL exibida.</span><span class="sxs-lookup"><span data-stu-id="4a680-212">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="4a680-213">Insira uma mensagem de teste e selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4a680-213">Enter a test message and select **Send**.</span></span> <span data-ttu-id="4a680-214">Quando terminar, selecione **Fechar Soquete**.</span><span class="sxs-lookup"><span data-stu-id="4a680-214">When done, select **Close Socket**.</span></span> <span data-ttu-id="4a680-215">A seção **Log de Comunicação** relata cada ação de abertura, envio e fechamento conforme ela ocorre.</span><span class="sxs-lookup"><span data-stu-id="4a680-215">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Estado inicial da página da Web](websockets/_static/end.png)
