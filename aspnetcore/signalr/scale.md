---
title: Hospedagem e dimensionamento de produção de ASP.NET Core SignalR
author: bradygaster
description: Saiba como evitar problemas de desempenho e dimensionamento em aplicativos que usam ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
no-loc:
- SignalR
ms.openlocfilehash: a215ce489746a7585cf4e72d4f04e0eac588b44c
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905720"
---
# <a name="aspnet-core-opno-locsignalr-hosting-and-scaling"></a>ASP.NET Core a hospedagem e o dimensionamento de SignalR

Por [Andrew Stanton-enfermaria](https://twitter.com/anurse), [Brady GASTER](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),

Este artigo explica as considerações de hospedagem e dimensionamento para aplicativos de alto tráfego que usam ASP.NET Core SignalR.

## <a name="sticky-sessions"></a>Sessões adesivas

SignalR requer que todas as solicitações HTTP para uma conexão específica sejam manipuladas pelo mesmo processo de servidor. Quando SignalR está em execução em um farm de servidores (vários servidores), "sessões adesivas" devem ser usadas. "Sessões adesivas" também são chamadas de afinidade de sessão por alguns balanceadores de carga. Azure App serviço usa [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) para rotear solicitações. Habilitar a configuração "afinidade de ARR" em seu serviço de Azure App permitirá "sessões adesivas". As únicas circunstâncias nas quais as sessões adesivas não são necessárias são:

1. Ao hospedar em um único servidor, em um único processo.
1. Ao usar o serviço de SignalR do Azure.
1. Quando todos os clientes são configurados para usar **apenas** WebSockets **e** a [configuração SkipNegotiation](xref:signalr/configuration#configure-additional-options) está habilitada na configuração do cliente.

Em todas as outras circunstâncias (incluindo quando o backplane Redis é usado), o ambiente de servidor deve ser configurado para sessões adesivas.

Para obter diretrizes sobre como configurar o serviço de Azure App para SignalR, consulte <xref:signalr/publish-to-azure-web-app>.

## <a name="tcp-connection-resources"></a>Recursos de conexão TCP

O número de conexões TCP simultâneas às quais um servidor Web pode dar suporte é limitado. Os clientes HTTP padrão usam conexões *efêmeras* . Essas conexões podem ser fechadas quando o cliente fica ocioso e reaberto mais tarde. Por outro lado, uma conexão SignalR é *persistente*. SignalR conexões permanecem abertas mesmo quando o cliente fica ocioso. Em um aplicativo de alto tráfego que atende a vários clientes, essas conexões persistentes podem fazer com que os servidores atinjam seu número máximo de conexões.

As conexões persistentes também consomem memória adicional para acompanhar cada conexão.

O uso intensivo de recursos relacionados à conexão por SignalR pode afetar outros aplicativos Web hospedados no mesmo servidor. Quando SignalR abre e mantém as últimas conexões TCP disponíveis, outros aplicativos Web no mesmo servidor também não têm mais conexões disponíveis.

Se um servidor ficar sem conexões, você verá erros de soquete aleatórios e erros de redefinição de conexão. Por exemplo:

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

O backplane Redis é a abordagem de expansão recomendada para aplicativos hospedados em sua própria infraestrutura. O serviço de SignalR do Azure não é uma opção prática para uso de produção com aplicativos locais devido à latência de conexão entre o data center e um data center do Azure.

As vantagens do serviço de SignalR do Azure observadas anteriormente são desvantagens do Redis backplane:

* As sessões adesivas, também conhecidas como [afinidade de cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), são necessárias. Depois que uma conexão é iniciada em um servidor, a conexão precisa permanecer nesse servidor.
* Um aplicativo SignalR deve escalar horizontalmente com base no número de clientes, mesmo que poucas mensagens estejam sendo enviadas.
* Um aplicativo SignalR usa significativamente mais recursos de conexão do que um aplicativo Web sem SignalR.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Documentação do serviço de SignalR do Azure](/azure/azure-signalr/signalr-overview)
* [Configurar um backplane Redis](xref:signalr/redis-backplane)
