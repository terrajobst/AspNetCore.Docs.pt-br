---
title: ASP.NET Core SignalR production hosting and scaling
author: bradygaster
description: Learn how to avoid performance and scaling problems in apps that use ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- SignalR
uid: signalr/scale
ms.openlocfilehash: 2ffafd452af46b635f4ebbdf74561ad043158808
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294730"
---
# <a name="aspnet-core-opno-locsignalr-hosting-and-scaling"></a><span data-ttu-id="0a1fb-103">ASP.NET Core SignalR hosting and scaling</span><span class="sxs-lookup"><span data-stu-id="0a1fb-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="0a1fb-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="0a1fb-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="0a1fb-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="0a1fb-106">Sticky Sessions</span><span class="sxs-lookup"><span data-stu-id="0a1fb-106">Sticky Sessions</span></span>

SignalR<span data-ttu-id="0a1fb-107"> requires that all HTTP requests for a specific connection be handled by the same server process.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-107"> requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="0a1fb-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="0a1fb-109">"Sticky sessions" are also called session affinity by some load balancers.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="0a1fb-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="0a1fb-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span><span class="sxs-lookup"><span data-stu-id="0a1fb-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="0a1fb-112">The only circumstances in which sticky sessions are not required are:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="0a1fb-113">When hosting on a single server, in a single process.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="0a1fb-114">When using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="0a1fb-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="0a1fb-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="0a1fb-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="0a1fb-118">TCP connection resources</span><span class="sxs-lookup"><span data-stu-id="0a1fb-118">TCP connection resources</span></span>

<span data-ttu-id="0a1fb-119">The number of concurrent TCP connections that a web server can support is limited.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="0a1fb-120">Standard HTTP clients use *ephemeral* connections.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="0a1fb-121">These connections can be closed when the client goes idle and reopened later.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="0a1fb-122">On the other hand, a SignalR connection is *persistent*.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-122">On the other hand, a SignalR connection is *persistent*.</span></span> SignalR<span data-ttu-id="0a1fb-123"> connections stay open even when the client goes idle.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-123"> connections stay open even when the client goes idle.</span></span> <span data-ttu-id="0a1fb-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="0a1fb-125">Persistent connections also consume some additional memory, to track each connection.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="0a1fb-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="0a1fb-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="0a1fb-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="0a1fb-129">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="0a1fb-130">Para manter SignalR uso de recursos de causar erros em outros aplicativos Web, execute SignalR em servidores diferentes dos outros aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="0a1fb-131">Para manter SignalR uso de recursos de causar erros em um aplicativo SignalR, escale horizontalmente para limitar o número de conexões que um servidor tem para lidar.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="0a1fb-132">Expansão do</span><span class="sxs-lookup"><span data-stu-id="0a1fb-132">Scale out</span></span>

<span data-ttu-id="0a1fb-133">Um aplicativo que usa o SignalR precisa manter o controle de todas as suas conexões, o que cria problemas para um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="0a1fb-134">Adicione um servidor e obtenha novas conexões que os outros servidores não conhecem.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="0a1fb-135">Por exemplo, SignalR em cada servidor no diagrama a seguir não está ciente das conexões nos outros servidores.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="0a1fb-136">Quando SignalR em um dos servidores deseja enviar uma mensagem para todos os clientes, a mensagem vai apenas para os clientes conectados a esse servidor.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Dimensionamento [! Parar. Não-LOC (Signalr)] sem um backplane](scale/_static/scale-no-backplane.png)

<span data-ttu-id="0a1fb-138">As opções para resolver esse problema são o [serviço de SignalR do Azure](#azure-signalr-service) e o [backplane Redis](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="0a1fb-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="0a1fb-139">Serviço de SignalR do Azure</span><span class="sxs-lookup"><span data-stu-id="0a1fb-139">Azure SignalR Service</span></span>

<span data-ttu-id="0a1fb-140">O serviço de SignalR do Azure é um proxy em vez de um backplane.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="0a1fb-141">Cada vez que um cliente inicia uma conexão com o servidor, o cliente é redirecionado para se conectar ao serviço.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="0a1fb-142">Esse processo é ilustrado no diagrama a seguir:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-142">That process is illustrated in the following diagram:</span></span>

![Estabelecendo uma conexão com o Azure [! Parar. Serviço não LOC (Signalr)]](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="0a1fb-144">O resultado é que o serviço gerencia todas as conexões de cliente, enquanto cada servidor precisa apenas de um pequeno número constante de conexões com o serviço, conforme mostrado no diagrama a seguir:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Clientes conectados ao serviço, servidores conectados ao serviço](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="0a1fb-146">Essa abordagem de expansão tem várias vantagens em relação à alternativa do backplane Redis:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="0a1fb-147">As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), não são necessárias, porque os clientes são redirecionados imediatamente para o serviço de SignalR do Azure quando se conectam.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="0a1fb-148">Um aplicativo SignalR pode escalar horizontalmente com base no número de mensagens enviadas, enquanto o serviço de SignalR do Azure é dimensionado automaticamente para lidar com qualquer número de conexões.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service automatically scales to handle any number of connections.</span></span> <span data-ttu-id="0a1fb-149">Por exemplo, pode haver milhares de clientes, mas se apenas algumas mensagens por segundo forem enviadas, o aplicativo SignalR não precisará escalar horizontalmente para vários servidores apenas para lidar com as próprias conexões.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="0a1fb-150">Um aplicativo SignalR não usará significativamente mais recursos de conexão do que um aplicativo Web sem SignalR.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="0a1fb-151">Por esses motivos, recomendamos o serviço de SignalR do Azure para todos os aplicativos ASP.NET Core SignalR hospedados no Azure, incluindo o serviço de aplicativo, as VMs e os contêineres.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="0a1fb-152">Para obter mais informações, consulte a [documentação do serviço de SignalR do Azure](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="0a1fb-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="0a1fb-153">Backplane de Redis</span><span class="sxs-lookup"><span data-stu-id="0a1fb-153">Redis backplane</span></span>

<span data-ttu-id="0a1fb-154">[Redis](https://redis.io/) é um repositório de chave-valor na memória que dá suporte a um sistema de mensagens com um modelo de publicação/assinatura.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="0a1fb-155">O SignalR backplane Redis usa o recurso pub/sub para encaminhar mensagens para outros servidores.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="0a1fb-156">Quando um cliente faz uma conexão, as informações de conexão são passadas para o backplane.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="0a1fb-157">Quando um servidor deseja enviar uma mensagem a todos os clientes, ele envia ao backplane.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="0a1fb-158">O backplane conhece todos os clientes conectados e em quais servidores eles estão.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="0a1fb-159">Ele envia a mensagem a todos os clientes por meio de seus respectivos servidores.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="0a1fb-160">Esse processo é ilustrado no diagrama a seguir:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-160">This process is illustrated in the following diagram:</span></span>

![Redis backplane, mensagem enviada de um servidor para todos os clientes](scale/_static/redis-backplane.png)

<span data-ttu-id="0a1fb-162">O backplane Redis é a abordagem de expansão recomendada para aplicativos hospedados em sua própria infraestrutura.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="0a1fb-163">Se houver uma latência de conexão significativa entre o data center e um data center do Azure, o serviço de SignalR do Azure poderá não ser uma opção prática para aplicativos locais com requisitos de baixa latência ou alta taxa de transferência.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="0a1fb-164">As vantagens do serviço de SignalR do Azure observadas anteriormente são desvantagens do Redis backplane:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="0a1fb-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="0a1fb-166">All clients are configured to **only** use WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="0a1fb-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="0a1fb-168">Once a connection is initiated on a server, the connection has to stay on that server.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="0a1fb-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="0a1fb-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="0a1fb-171">IIS limitations on Windows client OS</span><span class="sxs-lookup"><span data-stu-id="0a1fb-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="0a1fb-172">Windows 10 and Windows 8.x are client operating systems.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="0a1fb-173">IIS on client operating systems has a limit of 10 concurrent connections.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> SignalR<span data-ttu-id="0a1fb-174">'s connections are:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-174">'s connections are:</span></span>

* <span data-ttu-id="0a1fb-175">Transient and frequently re-established.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="0a1fb-176">**Not** disposed immediately when no longer used.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="0a1fb-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="0a1fb-178">When a client OS is used for development, we recommend:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="0a1fb-179">Avoid IIS.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-179">Avoid IIS.</span></span>
* <span data-ttu-id="0a1fb-180">Use Kestrel or IIS Express as deployment targets.</span><span class="sxs-lookup"><span data-stu-id="0a1fb-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="0a1fb-181">Linux com o Nginx</span><span class="sxs-lookup"><span data-stu-id="0a1fb-181">Linux with Nginx</span></span>

<span data-ttu-id="0a1fb-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="0a1fb-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="0a1fb-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0a1fb-184">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="0a1fb-184">Next steps</span></span>

<span data-ttu-id="0a1fb-185">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="0a1fb-185">For more information, see the following resources:</span></span>

* <span data-ttu-id="0a1fb-186">[Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="0a1fb-186">[Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview)</span></span>
* [<span data-ttu-id="0a1fb-187">Set up a Redis backplane</span><span class="sxs-lookup"><span data-stu-id="0a1fb-187">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
