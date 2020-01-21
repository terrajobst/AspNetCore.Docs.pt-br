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
# <a name="aspnet-core-opno-locsignalr-hosting-and-scaling"></a>ASP.NET Core SignalR hosting and scaling

By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),

This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.

## <a name="sticky-sessions"></a>Sticky Sessions

SignalR requires that all HTTP requests for a specific connection be handled by the same server process. When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used. "Sticky sessions" are also called session affinity by some load balancers. Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests. Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions". The only circumstances in which sticky sessions are not required are:

1. When hosting on a single server, in a single process.
1. When using the Azure SignalR Service.
1. When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.

In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.

For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>TCP connection resources

The number of concurrent TCP connections that a web server can support is limited. Standard HTTP clients use *ephemeral* connections. These connections can be closed when the client goes idle and reopened later. On the other hand, a SignalR connection is *persistent*. SignalR connections stay open even when the client goes idle. In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.

Persistent connections also consume some additional memory, to track each connection.

The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server. When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.

If a server runs out of connections, you'll see random socket errors and connection reset errors. Por exemplo:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Para manter SignalR uso de recursos de causar erros em outros aplicativos Web, execute SignalR em servidores diferentes dos outros aplicativos Web.

Para manter SignalR uso de recursos de causar erros em um aplicativo SignalR, escale horizontalmente para limitar o número de conexões que um servidor tem para lidar.

## <a name="scale-out"></a>Expansão do

Um aplicativo que usa o SignalR precisa manter o controle de todas as suas conexões, o que cria problemas para um farm de servidores. Adicione um servidor e obtenha novas conexões que os outros servidores não conhecem. Por exemplo, SignalR em cada servidor no diagrama a seguir não está ciente das conexões nos outros servidores. Quando SignalR em um dos servidores deseja enviar uma mensagem para todos os clientes, a mensagem vai apenas para os clientes conectados a esse servidor.

![Dimensionamento [! Parar. Não-LOC (Signalr)] sem um backplane](scale/_static/scale-no-backplane.png)

As opções para resolver esse problema são o [serviço de SignalR do Azure](#azure-signalr-service) e o [backplane Redis](#redis-backplane).

## <a name="azure-opno-locsignalr-service"></a>Serviço de SignalR do Azure

O serviço de SignalR do Azure é um proxy em vez de um backplane. Cada vez que um cliente inicia uma conexão com o servidor, o cliente é redirecionado para se conectar ao serviço. Esse processo é ilustrado no diagrama a seguir:

![Estabelecendo uma conexão com o Azure [! Parar. Serviço não LOC (Signalr)]](scale/_static/azure-signalr-service-one-connection.png)

O resultado é que o serviço gerencia todas as conexões de cliente, enquanto cada servidor precisa apenas de um pequeno número constante de conexões com o serviço, conforme mostrado no diagrama a seguir:

![Clientes conectados ao serviço, servidores conectados ao serviço](scale/_static/azure-signalr-service-multiple-connections.png)

Essa abordagem de expansão tem várias vantagens em relação à alternativa do backplane Redis:

* As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), não são necessárias, porque os clientes são redirecionados imediatamente para o serviço de SignalR do Azure quando se conectam.
* Um aplicativo SignalR pode escalar horizontalmente com base no número de mensagens enviadas, enquanto o serviço de SignalR do Azure é dimensionado automaticamente para lidar com qualquer número de conexões. Por exemplo, pode haver milhares de clientes, mas se apenas algumas mensagens por segundo forem enviadas, o aplicativo SignalR não precisará escalar horizontalmente para vários servidores apenas para lidar com as próprias conexões.
* Um aplicativo SignalR não usará significativamente mais recursos de conexão do que um aplicativo Web sem SignalR.

Por esses motivos, recomendamos o serviço de SignalR do Azure para todos os aplicativos ASP.NET Core SignalR hospedados no Azure, incluindo o serviço de aplicativo, as VMs e os contêineres.

Para obter mais informações, consulte a [documentação do serviço de SignalR do Azure](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Backplane de Redis

[Redis](https://redis.io/) é um repositório de chave-valor na memória que dá suporte a um sistema de mensagens com um modelo de publicação/assinatura. O SignalR backplane Redis usa o recurso pub/sub para encaminhar mensagens para outros servidores. Quando um cliente faz uma conexão, as informações de conexão são passadas para o backplane. Quando um servidor deseja enviar uma mensagem a todos os clientes, ele envia ao backplane. O backplane conhece todos os clientes conectados e em quais servidores eles estão. Ele envia a mensagem a todos os clientes por meio de seus respectivos servidores. Esse processo é ilustrado no diagrama a seguir:

![Redis backplane, mensagem enviada de um servidor para todos os clientes](scale/_static/redis-backplane.png)

O backplane Redis é a abordagem de expansão recomendada para aplicativos hospedados em sua própria infraestrutura. Se houver uma latência de conexão significativa entre o data center e um data center do Azure, o serviço de SignalR do Azure poderá não ser uma opção prática para aplicativos locais com requisitos de baixa latência ou alta taxa de transferência.

As vantagens do serviço de SignalR do Azure observadas anteriormente são desvantagens do Redis backplane:

* Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:
  * All clients are configured to **only** use WebSockets.
  * The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration. 
   Once a connection is initiated on a server, the connection has to stay on that server.
* A SignalR app must scale out based on number of clients even if few messages are being sent.
* A SignalR app uses significantly more connection resources than a web app without SignalR.

## <a name="iis-limitations-on-windows-client-os"></a>IIS limitations on Windows client OS

Windows 10 and Windows 8.x are client operating systems. IIS on client operating systems has a limit of 10 concurrent connections. SignalR's connections are:

* Transient and frequently re-established.
* **Not** disposed immediately when no longer used.

The preceding conditions make it likely to hit the 10 connection limit on a client OS. When a client OS is used for development, we recommend:

* Avoid IIS.
* Use Kestrel or IIS Express as deployment targets.

## <a name="linux-with-nginx"></a>Linux com o Nginx

Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para obter mais informações, consulte os seguintes recursos:

* [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview)
* [Set up a Redis backplane](xref:signalr/redis-backplane)
