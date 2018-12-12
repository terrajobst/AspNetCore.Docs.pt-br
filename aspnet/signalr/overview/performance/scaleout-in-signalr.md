---
uid: signalr/overview/performance/scaleout-in-signalr
title: Introdução à expansão no SignalR | Microsoft Docs
author: MikeWasson
description: Versões de software usado neste tópico, o Visual Studio 2013 .NET 4.5 SignalR versões anteriores de versão 2 deste tópico para obter informações sobre versões anteriores do...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 7e781fc1-1c1f-45a8-bc1d-338e96dbe9c9
msc.legacyurl: /signalr/overview/performance/scaleout-in-signalr
msc.type: authoredcontent
ms.openlocfilehash: 50f9e5bc2713af7fe41473339e360099a92d4c5d
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286981"
---
<a name="introduction-to-scaleout-in-signalr"></a>Introdução à expansão no SignalR
====================
por [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a>Versões de software usadas neste tópico
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Versão 2 do SignalR
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Versões anteriores deste tópico
>
> Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


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

Se você implantar seu aplicativo no Azure, considere o uso de backplane do Redis usando [Cache Redis do Azure](https://azure.microsoft.com/services/cache/). Se você estiver implantando em seu próprio farm de servidores, considere o SQL Server ou o Redis backplanes.

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

- [Servidor de transmissão](../getting-started/tutorial-server-broadcast-with-signalr.md) (por exemplo, bolsa): Backplanes funcionam bem para este cenário, porque o servidor controla a taxa na qual as mensagens são enviadas.
- [Cliente-para-cliente](../getting-started/tutorial-getting-started-with-signalr.md) (por exemplo, bate-papo): Nesse cenário, o backplane pode ser um gargalo se o número de mensagens pode ser dimensionado com o número de clientes. ou seja, se a taxa de mensagens aumenta proporcionalmente de mais clientes unir.
- [Em tempo real de alta frequência](../getting-started/tutorial-high-frequency-realtime-with-signalr.md) (por exemplo, jogos em tempo real): Um backplane não é recomendado para este cenário.

## <a name="enabling-tracing-for-signalr-scaleout"></a>Habilitação do rastreamento de expansão do SignalR

Para habilitar o rastreamento para o backplanes, adicione as seções a seguir ao arquivo Web. config, sob a raiz **configuração** elemento:

[!code-html[Main](scaleout-in-signalr/samples/sample1.html)]
