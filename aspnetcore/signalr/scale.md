---
title: Hospedagem e dimensionamento de produção de ASP.NET Core SignalR
author: bradygaster
description: Saiba como evitar problemas de desempenho e dimensionamento em aplicativos que usam ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- SignalR
uid: signalr/scale
ms.openlocfilehash: bb32bb8617f8a3e4170eeb7e38696ee2bbcafe03
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172549"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>Hospedagem e dimensionamento de ASP.NET Core Signalr

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse), [Brady GASTER](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),

Este artigo explica as considerações de hospedagem e dimensionamento para aplicativos de alto tráfego que usam ASP.NET Core Signalr.

## <a name="sticky-sessions"></a>Sessões adesivas

O signalr requer que todas as solicitações HTTP de uma conexão específica sejam manipuladas pelo mesmo processo do servidor. Quando o Signalr está em execução em um farm de servidores (vários servidores), "sessões adesivas" devem ser usadas. "Sessões adesivas" também são chamadas de afinidade de sessão por alguns balanceadores de carga. Azure App serviço usa [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) para rotear solicitações. Habilitar a configuração "afinidade de ARR" em seu serviço de Azure App permitirá "sessões adesivas". As únicas circunstâncias nas quais as sessões adesivas não são necessárias são:

1. Ao hospedar em um único servidor, em um único processo.
1. Ao usar o serviço de Signaler do Azure.
1. Quando todos os clientes são configurados para usar **apenas** WebSockets **e** a [configuração SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitada na configuração do cliente.

Em todas as outras circunstâncias (incluindo quando o backplane Redis é usado), o ambiente de servidor deve ser configurado para sessões adesivas.

Para obter orientação sobre como configurar o serviço de Azure App para o Signalr, consulte <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>Recursos de conexão TCP

O número de conexões TCP simultâneas às quais um servidor Web pode dar suporte é limitado. Os clientes HTTP padrão usam conexões *efêmeras* . Essas conexões podem ser fechadas quando o cliente fica ocioso e reaberto mais tarde. Por outro lado, uma conexão de Signalr é *persistente*. As conexões do signalr permanecem abertas mesmo quando o cliente fica ocioso. Em um aplicativo de alto tráfego que atende a vários clientes, essas conexões persistentes podem fazer com que os servidores atinjam seu número máximo de conexões.

As conexões persistentes também consomem memória adicional para acompanhar cada conexão.

O uso intensivo de recursos relacionados à conexão por Signalr pode afetar outros aplicativos Web hospedados no mesmo servidor. Quando o Signalr é aberto e mantém as últimas conexões TCP disponíveis, outros aplicativos Web no mesmo servidor também não têm mais conexões disponíveis.

Se um servidor ficar sem conexões, você verá erros de soquete aleatórios e erros de redefinição de conexão. Por exemplo:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Para manter o uso de recursos do Signalr de causar erros em outros aplicativos Web, execute o Signalr em servidores diferentes dos outros aplicativos Web.

Para manter o uso de recursos do Signalr de causar erros em um aplicativo Signalr, escale horizontalmente para limitar o número de conexões que um servidor tem para lidar.

## <a name="scale-out"></a>Expansão

Um aplicativo que usa o Signalr precisa manter o controle de todas as suas conexões, o que cria problemas para um farm de servidores. Adicione um servidor e obtenha novas conexões que os outros servidores não conhecem. Por exemplo, o Signalr em cada servidor no diagrama a seguir não reconhece as conexões nos outros servidores. Quando o Signalr em um dos servidores quiser enviar uma mensagem a todos os clientes, a mensagem vai apenas para os clientes conectados a esse servidor.

![Dimensionando o Signalr sem um backplane](scale/_static/scale-no-backplane.png)

As opções para resolver esse problema são o [serviço de signaler do Azure](#azure-signalr-service) e o [backplane Redis](#redis-backplane).

## <a name="azure-signalr-service"></a>Serviço Azure SignalR

O serviço de sinalizador do Azure é um proxy em vez de um backplane. Cada vez que um cliente inicia uma conexão com o servidor, o cliente é redirecionado para se conectar ao serviço. Esse processo é ilustrado no diagrama a seguir:

![Estabelecendo uma conexão com o serviço de sinalizador do Azure](scale/_static/azure-signalr-service-one-connection.png)

O resultado é que o serviço gerencia todas as conexões de cliente, enquanto cada servidor precisa apenas de um pequeno número constante de conexões com o serviço, conforme mostrado no diagrama a seguir:

![Clientes conectados ao serviço, servidores conectados ao serviço](scale/_static/azure-signalr-service-multiple-connections.png)

Essa abordagem de expansão tem várias vantagens em relação à alternativa do backplane Redis:

* As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), não são necessárias, porque os clientes são redirecionados imediatamente para o serviço de Signaler do Azure quando se conectam.
* Um aplicativo Signalr pode escalar horizontalmente com base no número de mensagens enviadas, enquanto o serviço do Azure Signalr é dimensionado automaticamente para lidar com qualquer número de conexões. Por exemplo, pode haver milhares de clientes, mas se apenas algumas mensagens por segundo forem enviadas, o aplicativo Signalr não precisará escalar horizontalmente para vários servidores apenas para lidar com as próprias conexões.
* Um aplicativo de sinalização não usará significativamente mais recursos de conexão do que um aplicativo Web sem Signalr.

Por esses motivos, recomendamos o serviço de Signalr do Azure para todos os ASP.NET Core aplicativos de sinalização hospedados no Azure, incluindo serviço de aplicativo, VMs e contêineres.

Para obter mais informações, consulte a [documentação do serviço de signaler do Azure](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Backplane de Redis

[Redis](https://redis.io/) é um repositório de chave-valor na memória que dá suporte a um sistema de mensagens com um modelo de publicação/assinatura. O backplane Redis do Signalr usa o recurso pub/sub para encaminhar mensagens para outros servidores. Quando um cliente faz uma conexão, as informações de conexão são passadas para o backplane. Quando um servidor deseja enviar uma mensagem a todos os clientes, ele envia ao backplane. O backplane conhece todos os clientes conectados e em quais servidores eles estão. Ele envia a mensagem a todos os clientes por meio de seus respectivos servidores. Esse processo é ilustrado no diagrama a seguir:

![Redis backplane, mensagem enviada de um servidor para todos os clientes](scale/_static/redis-backplane.png)

O backplane Redis é a abordagem de expansão recomendada para aplicativos hospedados em sua própria infraestrutura. Se houver uma latência de conexão significativa entre o data center e um data center do Azure, o serviço de Signaler do Azure poderá não ser uma opção prática para aplicativos locais com requisitos de baixa latência ou alta taxa de transferência.

As vantagens do serviço de Signaler do Azure observadas anteriormente são desvantagens do Redis backplane:

* As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), são necessárias, exceto quando **ambas** das seguintes opções são verdadeiras:
  * Todos os clientes estão configurados para usar **apenas** WebSockets.
  * A [configuração SkipNegotiation](xref:signalr/configuration#configure-additional-options) é habilitada na configuração do cliente. 
   Depois que uma conexão é iniciada em um servidor, a conexão precisa permanecer nesse servidor.
* Um aplicativo SignalR deve escalar horizontalmente com base no número de clientes, mesmo que poucas mensagens estejam sendo enviadas.
* Um aplicativo SignalR usa significativamente mais recursos de conexão do que um aplicativo Web sem SignalR.

## <a name="iis-limitations-on-windows-client-os"></a>Limitações do IIS no sistema operacional do cliente Windows

O Windows 10 e o Windows 8. x são sistemas operacionais cliente. O IIS em sistemas operacionais cliente tem um limite de 10 conexões simultâneas. as conexões do SignalRsão:

* Transitório e frequentemente restabelecida.
* **Não** Descartado imediatamente quando não é mais usado.

As condições anteriores tornam possível atingir o limite de 10 conexões em um sistema operacional cliente. Quando um sistema operacional cliente é usado para desenvolvimento, recomendamos:

* Evite o IIS.
* Use Kestrel ou IIS Express como destinos de implantação.

## <a name="linux-with-nginx"></a>Linux com o Nginx

Defina os cabeçalhos de `Connection` e `Upgrade` do proxy para o seguinte para SignalR WebSockets:

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

Para obter mais informações, consulte [Nginx como um proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Documentação do serviço de SignalR do Azure](/azure/azure-signalr/signalr-overview)
* [Configurar um backplane Redis](xref:signalr/redis-backplane)
