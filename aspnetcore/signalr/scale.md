---
title: Hospedagem de produção do ASP.NET SignalR Core e dimensionamento
author: bradygaster
description: Saiba como evitar o desempenho e dimensionamento problemas em aplicativos que usam o SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
ms.openlocfilehash: 4ac4509acc89d0091a3757c7cfbc9981614f29ad
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895073"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>Hospedagem do ASP.NET SignalR Core e dimensionamento

Por [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), e [Tom Dykstra](https://github.com/tdykstra),

Este artigo explica as considerações para aplicativos de alto tráfego que usam o SignalR do ASP.NET Core de dimensionamento e hospedagem.

## <a name="tcp-connection-resources"></a>Recursos de conexão TCP

O número de conexões TCP simultâneas que pode dar suporte a um servidor web é limitado. Os clientes HTTP padrão usam *efêmero* conexões. Essas conexões podem ser fechadas quando o cliente fica ocioso e reabrir posteriormente. Por outro lado, uma conexão SignalR é *persistente*. Conexões do SignalR permanecem aberta até mesmo quando o cliente vai ocioso. Em um aplicativo de alto tráfego que atende a muitos clientes, essas conexões persistentes podem causar servidores atingido o número máximo de conexões.

Conexões persistentes também consomem memória extra, para acompanhar cada conexão.

O uso intenso de recursos relacionados à conexão pelo SignalR pode afetar outros aplicativos web hospedados no mesmo servidor. Quando o SignalR é aberto e mantém as último conexões TCP disponíveis, outros aplicativos web no mesmo servidor também não tem mais conexões disponíveis para eles.

Se um servidor ficar sem conexões, você verá erros de soquete aleatória e erros de redefinição de conexão. Por exemplo:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Para evitar o uso de recursos do SignalR causando erros em outros aplicativos da web, execute o SignalR em servidores diferentes do que seus outros aplicativos da web.

Para evitar o uso de recursos do SignalR causando erros em um aplicativo do SignalR, escala horizontalmente para limitar o número de conexões que um servidor deve manipular.

## <a name="scale-out"></a>Expansão do

Um aplicativo que usa o SignalR precisa manter o controle de todas as suas conexões, que cria problemas para um farm de servidores. Adicionar um servidor, e ele obtém novas conexões de outros servidores não conhecer. Por exemplo, o SignalR em cada servidor no diagrama a seguir desconhece as conexões nos outros servidores. Quando quiser SignalR em um dos servidores enviar uma mensagem a todos os clientes, a mensagem é apenas vai para os clientes conectados a esse servidor.

![Dimensionamento SignalR sem um backplane](scale/_static/scale-no-backplane.png)

As opções para resolver esse problema são as [serviço do Azure SignalR](#azure-signalr-service) e [Redis backplane](#redis-backplane).

## <a name="azure-signalr-service"></a>Serviço Azure SignalR

O serviço do Azure SignalR é um proxy em vez de um backplane. Cada vez que um cliente inicia uma conexão ao servidor, o cliente é redirecionado para se conectar ao serviço. Esse processo é ilustrado no diagrama a seguir:

![Estabelecer uma conexão ao serviço Azure SignalR](scale/_static/azure-signalr-service-one-connection.png)

O resultado é que o serviço gerencia todas as conexões de cliente, enquanto cada servidor precisa de apenas um pequeno número constante de conexões para o serviço, conforme mostrado no diagrama a seguir:

![Clientes conectados ao serviço, servidores conectados ao serviço](scale/_static/azure-signalr-service-multiple-connections.png)

Essa abordagem de expansão tem várias vantagens em relação a alternativa de backplane do Redis:

* Sessões adesivas, também conhecidas como [afinidade do cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), não é necessário, pois os clientes imediatamente são redirecionados para o serviço do Azure SignalR quando eles se conectam.
* Um aplicativo pode escalar horizontalmente de SignalR com base no número de mensagens enviadas, enquanto o serviço do Azure SignalR é dimensionado automaticamente para lidar com qualquer número de conexões. Por exemplo, pode haver milhares de clientes, mas se apenas algumas mensagens por segundo são enviadas, o aplicativo SignalR não será necessário escalar horizontalmente em vários servidores apenas para lidar com as conexões em si.
* Um aplicativo de SignalR não usar significativamente mais recursos de conexão que um aplicativo web sem SignalR.

Por esses motivos, recomendamos que o serviço do Azure SignalR para todos os aplicativos do SignalR do ASP.NET Core hospedados no Azure, incluindo o serviço de aplicativo, as VMs e contêineres.

Para obter mais informações, consulte o [documentação do serviço do Azure SignalR](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Backplane de Redis

[Redis](https://redis.io/) é um repositório de chave-valor na memória que dá suporte a um sistema de mensagens com um modelo de publicação/assinatura. O backplane SignalR Redis usa o recurso de publicação/assinatura para encaminhar mensagens para outros servidores. Quando um cliente faz uma conexão, as informações de conexão são passadas ao backplane. Quando um servidor deseja enviar uma mensagem a todos os clientes, ele envia ao backplane. Backplane sabe clientes tudo conectados e quais servidores que eles estão. Ele envia a mensagem a todos os clientes por meio de seus respectivos servidores. Esse processo é ilustrado no diagrama a seguir:

![Backplane de mensagem enviada de um servidor para todos os clientes de redis](scale/_static/redis-backplane.png)

O backplane do Redis é a abordagem recomendada de escalabilidade horizontal para aplicativos hospedados em sua própria infraestrutura. Azure SignalR Service não é uma opção prática para uso em produção com aplicativos no local devido à latência de conexão entre seu data center e um data center do Azure.

As vantagens de serviço do Azure SignalR observadas anteriormente são as desvantagens para o backplane do Redis:

* Sessões adesivas, também conhecidas como [afinidade do cliente](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), é necessário. Depois que uma conexão é iniciada em um servidor, a conexão deve permanecer nesse servidor.
* Um aplicativo de SignalR deve escalar horizontalmente com base no número de clientes, mesmo se algumas mensagens estão sendo enviadas.
* Um aplicativo de SignalR usa significativamente mais recursos de conexão que um aplicativo web sem SignalR.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Documentação do SignalR Service do Azure](/azure/azure-signalr/signalr-overview)
* [Configurar um backplane de Redis](xref:signalr/redis-backplane)
