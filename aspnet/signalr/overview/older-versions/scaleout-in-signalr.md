---
uid: signalr/overview/older-versions/scaleout-in-signalr
title: Introdução à expansão no SignalR 1.x | Microsoft Docs
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 04/29/2013
ms.assetid: 3fd9f11c-799b-4001-bd60-1e70cfc61c19
msc.legacyurl: /signalr/overview/older-versions/scaleout-in-signalr
msc.type: authoredcontent
ms.openlocfilehash: 78e53c38ec760334cecee0431d52d993a657b908
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837280"
---
<a name="introduction-to-scaleout-in-signalr-1x"></a>Introdução à expansão no SignalR 1.x
====================
por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

Em geral, há duas maneiras para dimensionar um aplicativo web: *escalar verticalmente* e *expandir*.

- Escale verticalmente significa usar um servidor maior (ou uma VM maior) com mais RAM, CPUs, etc.
- Escalar horizontalmente significa adicionar mais servidores para lidar com a carga.

O problema com a expansão é que você rapidamente atinge um limite no tamanho da máquina. Além disso, você precisa escalar horizontalmente. No entanto, quando você escala horizontalmente, os clientes podem obter roteados para diferentes servidores. Um cliente que está conectado a um servidor não receberá as mensagens enviadas do outro servidor.

![](scaleout-in-signalr/_static/image1.png)

Uma solução é encaminhar mensagens entre servidores, usando um componente chamado um *backplane*. Com um backplane habilitado, cada instância do aplicativo envia mensagens ao backplane e backplane encaminha-os para as outras instâncias do aplicativo. (Em eletrônica, um backplane é um grupo de conectores paralelos. Por analogia, um backplane SignalR conecta vários servidores.)

![](scaleout-in-signalr/_static/image2.png)

Atualmente, o SignalR fornece três backplanes:

- **O barramento de serviço do Azure**. O barramento de serviço é uma infraestrutura de mensagens que permite que os componentes enviem mensagens de uma maneira menos rígida.
- **Redis**. O redis é um repositório de chave-valor na memória. Redis oferece suporte a um padrão de publicação/assinatura ("pub/sub") para enviar mensagens.
- **SQL Server**. Backplane do SQL Server grava mensagens em tabelas SQL. Backplane usa o Service Broker para o sistema de mensagens eficiente. No entanto, ele também funciona se o Service Broker não está habilitado.

Se você implantar seu aplicativo no Azure, considere o uso do backplane do barramento de serviço do Azure. Se você estiver implantando em seu próprio farm de servidores, considere o SQL Server ou o Redis backplanes.

Os tópicos a seguir contêm os tutoriais passo a passo para cada backplane:

- [Expansão do SignalR com o Barramento de Serviço do Azure](scaleout-with-windows-azure-service-bus.md)
- [Expansão do SignalR com Redis](scaleout-with-redis.md)
- [Expansão do SignalR com o SQL Server](scaleout-with-sql-server.md)

## <a name="implementation"></a>Implementação

No SignalR, cada mensagem é enviada por meio de um barramento de mensagem. Um barramento de mensagem implementa o [IMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) interface, que fornece uma abstração de publicação/assinatura. Os backplanes funcionam, substituindo o padrão **IMessageBus** com barramento projetado por esse backplane. Por exemplo, é o barramento de mensagem para Redis [RedisMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.redis.redismessagebus(v=vs.100).aspx), e usa o Redis [pub/sub](http://redis.io/topics/pubsub) mecanismo para enviar e receber mensagens.

Cada instância de servidor se conecta ao backplane por meio do barramento. Quando uma mensagem é enviada, ela vai para o backplane e o backplane envia para cada servidor. Quando um servidor recebe uma mensagem do backplane, ele coloca a mensagem em seu cache local. O servidor, em seguida, entrega mensagens para os clientes de seu cache local.

Para cada conexão de cliente, o progresso do cliente no fluxo de mensagens de leitura é controlado usando um cursor. (Um cursor representa uma posição no fluxo de mensagens.) Se um cliente se desconecta e, em seguida, reconecta-se, ele solicita o barramento de todas as mensagens que chegaram após o valor do cursor do cliente. A mesma coisa acontece quando uma conexão usa [pesquisa longa](../getting-started/introduction-to-signalr.md#transports). Após a conclusão de uma solicitação de sondagem longa, o cliente abre uma nova conexão e pede as mensagens que chegaram após o cursor.

A cursor mecanismo funciona mesmo se um cliente é roteado para um servidor diferente no se reconectar. O backplane está ciente de todos os servidores, e não importa qual servidor um cliente se conecta ao.

## <a name="limitations"></a>Limitações

Usando um backplane, a taxa de transferência máxima de mensagens é menor do que quando os clientes falam diretamente a um nó de servidor único. Isso ocorre porque o backplane encaminha todas as mensagens para todos os nós, para que o backplane pode se tornar um gargalo. Se essa limitação é um problema depende do aplicativo. Por exemplo, aqui estão alguns cenários típicos do SignalR:

- [Servidor de transmissão](tutorial-server-broadcast-with-aspnet-signalr.md) (por exemplo, bolsa): Backplanes funcionam bem para este cenário, porque o servidor controla a taxa na qual as mensagens são enviadas.
- [Cliente-para-cliente](tutorial-getting-started-with-signalr.md) (por exemplo, bate-papo): Nesse cenário, o backplane pode ser um gargalo se o número de mensagens pode ser dimensionado com o número de clientes. ou seja, se a taxa de mensagens aumenta proporcionalmente de mais clientes unir.
- [Em tempo real de alta frequência](tutorial-high-frequency-realtime-with-signalr.md) (por exemplo, jogos em tempo real): Um backplane não é recomendado para este cenário.

## <a name="enabling-tracing-for-signalr-scaleout"></a>Habilitação do rastreamento de expansão do SignalR

Para habilitar o rastreamento para o backplanes, adicione as seções a seguir ao arquivo Web. config, sob a raiz **configuração** elemento:

[!code-html[Main](scaleout-in-signalr/samples/sample1.html)]
