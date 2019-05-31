---
title: Suporte ao WebSockets no ASP.NET Core
author: rick-anderson
description: Saiba como começar a usar o WebSockets no ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/10/2019
uid: fundamentals/websockets
ms.openlocfilehash: bba9cf051deaf57efdd82ca2fb1318fce79bd6cc
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223214"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="84e47-103">Suporte ao WebSockets no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84e47-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="84e47-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="84e47-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="84e47-105">Este artigo explica como começar a usar o WebSockets no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="84e47-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="84e47-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) é um protocolo que permite canais de comunicação persistentes bidirecionais em conexões TCP.</span><span class="sxs-lookup"><span data-stu-id="84e47-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="84e47-107">Ele é usado em aplicativos que se beneficiam de comunicação rápida e em tempo real, como chat, painel e aplicativos de jogos.</span><span class="sxs-lookup"><span data-stu-id="84e47-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="84e47-108">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="84e47-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="84e47-109">Confira a seção [Próximas etapas](#next-steps) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="84e47-109">See the [Next steps](#next-steps) section for more information.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84e47-110">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="84e47-110">Prerequisites</span></span>

* <span data-ttu-id="84e47-111">ASP.NET Core 1.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="84e47-111">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="84e47-112">Qualquer sistema operacional compatível com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="84e47-112">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="84e47-113">Windows 7/Windows Server 2008 ou posterior</span><span class="sxs-lookup"><span data-stu-id="84e47-113">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="84e47-114">Linux</span><span class="sxs-lookup"><span data-stu-id="84e47-114">Linux</span></span>
  * <span data-ttu-id="84e47-115">macOS</span><span class="sxs-lookup"><span data-stu-id="84e47-115">macOS</span></span>
  
* <span data-ttu-id="84e47-116">Se o aplicativo é executado no Windows com o IIS:</span><span class="sxs-lookup"><span data-stu-id="84e47-116">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="84e47-117">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="84e47-117">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="84e47-118">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="84e47-118">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="84e47-119">WebSockets precisam ser habilitados (confira a seção [Suporte para IIS/IIS Express](#iisiis-express-support)).</span><span class="sxs-lookup"><span data-stu-id="84e47-119">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="84e47-120">Se o aplicativo é executado no [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="84e47-120">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="84e47-121">Windows 8/Windows Server 2012 ou posterior</span><span class="sxs-lookup"><span data-stu-id="84e47-121">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="84e47-122">Para saber quais são os navegadores compatíveis, confira https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="84e47-122">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="when-to-use-websockets"></a><span data-ttu-id="84e47-123">Quando usar WebSockets</span><span class="sxs-lookup"><span data-stu-id="84e47-123">When to use WebSockets</span></span>

<span data-ttu-id="84e47-124">Use WebSockets para trabalhar diretamente com uma conexão de soquete.</span><span class="sxs-lookup"><span data-stu-id="84e47-124">Use WebSockets to work directly with a socket connection.</span></span> <span data-ttu-id="84e47-125">Por exemplo, use WebSockets para obter o melhor desempenho possível em um jogo em tempo real.</span><span class="sxs-lookup"><span data-stu-id="84e47-125">For example, use WebSockets for the best possible performance with a real-time game.</span></span>

<span data-ttu-id="84e47-126">[SignalR do ASP.NET Core](xref:signalr/introduction) é uma biblioteca que simplifica a adição da funcionalidade da Web em tempo real aos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="84e47-126">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="84e47-127">Ele usa WebSockets sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="84e47-127">It uses WebSockets whenever possible.</span></span>

## <a name="how-to-use-websockets"></a><span data-ttu-id="84e47-128">Como usar WebSockets</span><span class="sxs-lookup"><span data-stu-id="84e47-128">How to use WebSockets</span></span>

* <span data-ttu-id="84e47-129">Instale o pacote [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).</span><span class="sxs-lookup"><span data-stu-id="84e47-129">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>
* <span data-ttu-id="84e47-130">Configure o middleware.</span><span class="sxs-lookup"><span data-stu-id="84e47-130">Configure the middleware.</span></span>
* <span data-ttu-id="84e47-131">Aceite solicitações do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="84e47-131">Accept WebSocket requests.</span></span>
* <span data-ttu-id="84e47-132">Envie e receba mensagens.</span><span class="sxs-lookup"><span data-stu-id="84e47-132">Send and receive messages.</span></span>

### <a name="configure-the-middleware"></a><span data-ttu-id="84e47-133">Configurar o middleware</span><span class="sxs-lookup"><span data-stu-id="84e47-133">Configure the middleware</span></span>

<span data-ttu-id="84e47-134">Adicione o middleware do WebSockets no método `Configure` da classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="84e47-134">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="84e47-135">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="84e47-135">The following settings can be configured:</span></span>

* <span data-ttu-id="84e47-136">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="84e47-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="84e47-137">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="84e47-137">The default is two minutes.</span></span>
* <span data-ttu-id="84e47-138">`ReceiveBufferSize` – o tamanho do buffer usado para receber dados.</span><span class="sxs-lookup"><span data-stu-id="84e47-138">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="84e47-139">Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados.</span><span class="sxs-lookup"><span data-stu-id="84e47-139">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="84e47-140">O padrão é 4 KB.</span><span class="sxs-lookup"><span data-stu-id="84e47-140">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="84e47-141">As seguintes configurações podem ser definidas:</span><span class="sxs-lookup"><span data-stu-id="84e47-141">The following settings can be configured:</span></span>

* <span data-ttu-id="84e47-142">`KeepAliveInterval` – a frequência para enviar quadros "ping" ao cliente para garantir que os proxies mantenham a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="84e47-142">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="84e47-143">O padrão é dois minutos.</span><span class="sxs-lookup"><span data-stu-id="84e47-143">The default is two minutes.</span></span>
* <span data-ttu-id="84e47-144">`ReceiveBufferSize` – o tamanho do buffer usado para receber dados.</span><span class="sxs-lookup"><span data-stu-id="84e47-144">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="84e47-145">Os usuários avançados podem precisar alterar isso para ajuste de desempenho com base no tamanho dos dados.</span><span class="sxs-lookup"><span data-stu-id="84e47-145">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="84e47-146">O padrão é 4 KB.</span><span class="sxs-lookup"><span data-stu-id="84e47-146">The default is 4 KB.</span></span>
* <span data-ttu-id="84e47-147">`AllowedOrigins` – Uma lista de valores de cabeçalho de origem permitidos para solicitações do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="84e47-147">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="84e47-148">Por padrão, todas as origens são permitidas.</span><span class="sxs-lookup"><span data-stu-id="84e47-148">By default, all origins are allowed.</span></span> <span data-ttu-id="84e47-149">Consulte "Restrição de origem do WebSocket" abaixo para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="84e47-149">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

::: moniker-end

### <a name="accept-websocket-requests"></a><span data-ttu-id="84e47-150">Aceitar solicitações do WebSocket</span><span class="sxs-lookup"><span data-stu-id="84e47-150">Accept WebSocket requests</span></span>

<span data-ttu-id="84e47-151">Em algum lugar e em um momento futuro no ciclo de vida da solicitação (posteriormente no método `Configure` ou em uma ação do MVC, por exemplo), verifique se é uma solicitação do WebSocket e aceite-a.</span><span class="sxs-lookup"><span data-stu-id="84e47-151">Somewhere later in the request life cycle (later in the `Configure` method or in an MVC action, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="84e47-152">O exemplo a seguir é de uma fase posterior do método `Configure`:</span><span class="sxs-lookup"><span data-stu-id="84e47-152">The following example is from later in the `Configure` method:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

::: moniker-end

<span data-ttu-id="84e47-153">Uma solicitação do WebSocket pode entrar em qualquer URL, mas esse código de exemplo aceita apenas solicitações de `/ws`.</span><span class="sxs-lookup"><span data-stu-id="84e47-153">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="84e47-154">Ao usar um WebSocket, você **deve** manter o pipeline de middleware em execução durante a conexão.</span><span class="sxs-lookup"><span data-stu-id="84e47-154">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="84e47-155">Se tentar enviar ou receber uma mensagem do WebSocket após o término do pipeline de middleware, você poderá receber uma exceção semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="84e47-155">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="84e47-156">Se você estiver usando um serviço em segundo plano para gravar dados em um WebSocket, mantenha o pipeline do middleware em execução.</span><span class="sxs-lookup"><span data-stu-id="84e47-156">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="84e47-157">Faça isso usando um <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="84e47-157">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="84e47-158">Passe o `TaskCompletionSource` ao seu serviço em segundo plano e faça-o chamar <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> quando terminar com o WebSocket.</span><span class="sxs-lookup"><span data-stu-id="84e47-158">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="84e47-159">Em seguida, `await` a propriedade <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante a solicitação.</span><span class="sxs-lookup"><span data-stu-id="84e47-159">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request.</span></span>

### <a name="send-and-receive-messages"></a><span data-ttu-id="84e47-160">Enviar e receber mensagens</span><span class="sxs-lookup"><span data-stu-id="84e47-160">Send and receive messages</span></span>

<span data-ttu-id="84e47-161">O método `AcceptWebSocketAsync` atualiza a conexão TCP para uma conexão WebSocket e fornece um objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="84e47-161">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="84e47-162">Use o objeto `WebSocket` para enviar e receber mensagens.</span><span class="sxs-lookup"><span data-stu-id="84e47-162">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="84e47-163">O código mostrado anteriormente que aceita a solicitação do WebSocket passa o objeto `WebSocket` para um método `Echo`.</span><span class="sxs-lookup"><span data-stu-id="84e47-163">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="84e47-164">O código recebe uma mensagem e envia de volta imediatamente a mesma mensagem.</span><span class="sxs-lookup"><span data-stu-id="84e47-164">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="84e47-165">As mensagens são enviadas e recebidas em um loop até que o cliente feche a conexão:</span><span class="sxs-lookup"><span data-stu-id="84e47-165">Messages are sent and received in a loop until the client closes the connection:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](websockets/samples/1.x/WebSocketsSample/Startup.cs?name=Echo)]

::: moniker-end

<span data-ttu-id="84e47-166">Ao aceitar a conexão WebSocket antes de iniciar o loop, o pipeline de middleware é encerrado.</span><span class="sxs-lookup"><span data-stu-id="84e47-166">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="84e47-167">Ao fechar o soquete, o pipeline é desenrolado.</span><span class="sxs-lookup"><span data-stu-id="84e47-167">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="84e47-168">Ou seja, a solicitação deixa de avançar no pipeline quando o WebSocket é aceito.</span><span class="sxs-lookup"><span data-stu-id="84e47-168">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="84e47-169">Quando o loop é concluído e o soquete é fechado, a solicitação continua a avançar no pipeline.</span><span class="sxs-lookup"><span data-stu-id="84e47-169">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="handle-client-disconnects"></a><span data-ttu-id="84e47-170">Tratar desconexões de cliente</span><span class="sxs-lookup"><span data-stu-id="84e47-170">Handle client disconnects</span></span>

<span data-ttu-id="84e47-171">O servidor não é informado automaticamente quando o cliente se desconecta devido à perda de conectividade.</span><span class="sxs-lookup"><span data-stu-id="84e47-171">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="84e47-172">O servidor recebe uma mensagem de desconexão apenas quando o cliente a envia, e isso é possível somente quando há conexão com a Internet.</span><span class="sxs-lookup"><span data-stu-id="84e47-172">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="84e47-173">Se quiser tomar alguma medida quando isso ocorrer, defina um tempo limite, caso não receba nada do cliente após uma determinada janela de tempo.</span><span class="sxs-lookup"><span data-stu-id="84e47-173">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="84e47-174">Se o cliente não envia mensagens com frequência, e você prefere não definir um tempo limite apenas porque a conexão fica ociosa, peça ao cliente para usar um temporizador a fim de enviar uma mensagem de ping a cada X segundos.</span><span class="sxs-lookup"><span data-stu-id="84e47-174">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="84e47-175">No servidor, se uma mensagem não chegar dentro de 2\*X segundos após a mensagem anterior, encerre a conexão e informe que o cliente se desconectou.</span><span class="sxs-lookup"><span data-stu-id="84e47-175">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="84e47-176">Aguarde o dobro do intervalo de tempo esperado a fim de deixar um tempo extra para atrasos na rede, que possam atrasar a mensagem de ping.</span><span class="sxs-lookup"><span data-stu-id="84e47-176">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

### <a name="websocket-origin-restriction"></a><span data-ttu-id="84e47-177">Restrição de origem do WebSocket</span><span class="sxs-lookup"><span data-stu-id="84e47-177">WebSocket origin restriction</span></span>

<span data-ttu-id="84e47-178">As proteções fornecidas pelo CORS não se aplicam ao WebSockets.</span><span class="sxs-lookup"><span data-stu-id="84e47-178">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="84e47-179">Navegadores **não**:</span><span class="sxs-lookup"><span data-stu-id="84e47-179">Browsers do **not**:</span></span>

* <span data-ttu-id="84e47-180">Executam solicitações de simulação de CORS.</span><span class="sxs-lookup"><span data-stu-id="84e47-180">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="84e47-181">Respeitam as restrições especificadas em cabeçalhos `Access-Control` ao fazer solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="84e47-181">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="84e47-182">No entanto, os navegadores enviam o cabeçalho `Origin` ao emitir solicitações de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="84e47-182">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="84e47-183">Os aplicativos devem ser configurados para validar esses cabeçalhos e garantir que apenas WebSockets provenientes de origens esperadas sejam permitidos.</span><span class="sxs-lookup"><span data-stu-id="84e47-183">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="84e47-184">Se você estiver hospedando o servidor em "https://server.com" e hospedando seu cliente em "https://client.com", adicione "https://client.com" à lista `AllowedOrigins` para o WebSockets verificar.</span><span class="sxs-lookup"><span data-stu-id="84e47-184">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="84e47-185">O cabeçalho `Origin` é controlado pelo cliente e, como o cabeçalho `Referer`, pode ser falsificado.</span><span class="sxs-lookup"><span data-stu-id="84e47-185">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="84e47-186">**Não** use esses cabeçalhos como um mecanismo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="84e47-186">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="84e47-187">Suporte ao IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="84e47-187">IIS/IIS Express support</span></span>

<span data-ttu-id="84e47-188">O Windows Server 2012 ou posterior e o Windows 8 ou posterior com o IIS/IIS Express 8 ou posterior são compatíveis com o protocolo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="84e47-188">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="84e47-189">WebSockets estão sempre habilitados ao usar o IIS Express.</span><span class="sxs-lookup"><span data-stu-id="84e47-189">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="84e47-190">Habilitar WebSockets no IIS</span><span class="sxs-lookup"><span data-stu-id="84e47-190">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="84e47-191">Para habilitar o suporte para o protocolo WebSocket no Windows Server 2012 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="84e47-191">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="84e47-192">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="84e47-192">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="84e47-193">Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor**.</span><span class="sxs-lookup"><span data-stu-id="84e47-193">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="84e47-194">Selecione **Instalação baseada em função ou em recurso**.</span><span class="sxs-lookup"><span data-stu-id="84e47-194">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="84e47-195">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="84e47-195">Select **Next**.</span></span>
1. <span data-ttu-id="84e47-196">Selecione o servidor apropriado (o servidor local é selecionado por padrão).</span><span class="sxs-lookup"><span data-stu-id="84e47-196">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="84e47-197">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="84e47-197">Select **Next**.</span></span>
1. <span data-ttu-id="84e47-198">Expanda **Servidor Web (IIS)** na árvore **Funções**, expanda **Servidor Web** e, em seguida, expanda **Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="84e47-198">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="84e47-199">Selecione o **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="84e47-199">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="84e47-200">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="84e47-200">Select **Next**.</span></span>
1. <span data-ttu-id="84e47-201">Se não forem necessários recursos adicionais, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="84e47-201">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="84e47-202">Clique em **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="84e47-202">Select **Install**.</span></span>
1. <span data-ttu-id="84e47-203">Quando a instalação for concluída, selecione **Fechar** para sair do assistente.</span><span class="sxs-lookup"><span data-stu-id="84e47-203">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="84e47-204">Para habilitar o suporte para o protocolo WebSocket no Windows 8 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="84e47-204">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="84e47-205">Estas etapas não são necessárias ao usar o IIS Express</span><span class="sxs-lookup"><span data-stu-id="84e47-205">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="84e47-206">Navegue para **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="84e47-206">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="84e47-207">Abra os seguintes nós: **Serviços de Informações da Internet** > **Serviços da World Wide Web** > **Recursos de Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="84e47-207">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="84e47-208">Selecione o recurso **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="84e47-208">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="84e47-209">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="84e47-209">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="84e47-210">Desabilite o WebSocket ao usar o socket.io no Node.js</span><span class="sxs-lookup"><span data-stu-id="84e47-210">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="84e47-211">Se você estiver usando o suporte do WebSocket no [socket.io](https://socket.io/) no [Node.js](https://nodejs.org/), desabilite o módulo do WebSocket do IIS padrão usando o elemento `webSocket` em *web.config* ou em *applicationHost.config*. Se essa etapa não for executada, o módulo do WebSocket do IIS tentará manipular a comunicação do WebSocket em vez do Node.js e o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84e47-211">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="next-steps"></a><span data-ttu-id="84e47-212">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="84e47-212">Next steps</span></span>

<span data-ttu-id="84e47-213">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompanha este artigo é um aplicativo de eco.</span><span class="sxs-lookup"><span data-stu-id="84e47-213">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="84e47-214">Ele tem uma página da Web que faz conexões WebSocket e o servidor reenvia para o cliente todas as mensagens recebidas.</span><span class="sxs-lookup"><span data-stu-id="84e47-214">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="84e47-215">Execute o aplicativo em um prompt de comando (ele não está configurado para execução no Visual Studio com o IIS Express) e navegue para http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="84e47-215">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="84e47-216">A página da Web exibe o status de conexão no canto superior esquerdo:</span><span class="sxs-lookup"><span data-stu-id="84e47-216">The web page shows the connection status in the upper left:</span></span>

![Estado inicial da página da Web](websockets/_static/start.png)

<span data-ttu-id="84e47-218">Selecione **Conectar** para enviar uma solicitação WebSocket para a URL exibida.</span><span class="sxs-lookup"><span data-stu-id="84e47-218">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="84e47-219">Insira uma mensagem de teste e selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="84e47-219">Enter a test message and select **Send**.</span></span> <span data-ttu-id="84e47-220">Quando terminar, selecione **Fechar Soquete**.</span><span class="sxs-lookup"><span data-stu-id="84e47-220">When done, select **Close Socket**.</span></span> <span data-ttu-id="84e47-221">A seção **Log de Comunicação** relata cada ação de abertura, envio e fechamento conforme ela ocorre.</span><span class="sxs-lookup"><span data-stu-id="84e47-221">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Estado inicial da página da Web](websockets/_static/end.png)
