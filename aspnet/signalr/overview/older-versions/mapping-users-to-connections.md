---
uid: signalr/overview/older-versions/mapping-users-to-connections
title: Mapeamento de usuários do SignalR para conexões no SignalR 1.x | Microsoft Docs
author: bradygaster
description: Este tópico mostra como reter informações sobre usuários e suas conexões.
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: ebbc93a8-e6c4-4122-8e0d-3aa42293c747
msc.legacyurl: /signalr/overview/older-versions/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 1e65a11e08a5b060cf8b096b5fe5b90eb8dc5b51
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836708"
---
<a name="mapping-signalr-users-to-connections-in-signalr-1x"></a>Mapeamento de usuários do SignalR para conexões no SignalR 1.x
====================
by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tópico mostra como reter informações sobre usuários e suas conexões.


## <a name="introduction"></a>Introdução

Cada cliente que se conecta a um hub passa uma id de conexão exclusivo. Você pode recuperar esse valor no `Context.ConnectionId` propriedade de contexto de hub. Se seu aplicativo precisar mapear um usuário para a id de conexão e manter esse mapeamento, você pode usar um dos seguintes:

- [Armazenamento na memória](#inmemory), como um dicionário
- [Grupo do SignalR para cada usuário](#groups)
- [Armazenamento externo permanente](#database), como uma tabela de banco de dados ou o armazenamento de tabelas do Azure

Cada uma dessas implementações é mostrada neste tópico. Você usa o `OnConnected`, `OnDisconnected`, e `OnReconnected` métodos do `Hub` classe para acompanhar o status de conexão do usuário.

A melhor abordagem para seu aplicativo depende de:

- O número de servidores web que hospedam seu aplicativo.
- Se você precisa obter uma lista dos usuários conectados no momento.
- Se você precisar persistir informações de usuário e grupo quando o aplicativo ou o servidor for reiniciado.
- Se a latência da chamada de um servidor externo é um problema.

A tabela a seguir mostra qual abordagem funciona para essas considerações.

|  | Mais de um servidor | Obter lista de usuários conectados no momento | Manter as informações depois que for reiniciado | Desempenho ideal |
| --- | --- | --- | --- | --- |
| Na memória |  | ![](mapping-users-to-connections/_static/image1.png) |  | ![](mapping-users-to-connections/_static/image2.png) |
| Grupos de usuário único | ![](mapping-users-to-connections/_static/image3.png) |  |  | ![](mapping-users-to-connections/_static/image4.png) |
| Permanentes, externas | ![](mapping-users-to-connections/_static/image5.png) | ![](mapping-users-to-connections/_static/image6.png) | ![](mapping-users-to-connections/_static/image7.png) |  |

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>Armazenamento na memória

Os exemplos a seguir mostram como reter informações de conexão e do usuário em um dicionário que é armazenado na memória. O dictionary usa um `HashSet` para armazenar a id de conexão. A qualquer momento, um usuário pode ter mais de uma conexão para o aplicativo do SignalR. Por exemplo, um usuário que está conectado por meio de vários dispositivos ou mais de uma guia do navegador teria mais de uma id de conexão.

Se o aplicativo é desligado, todas as informações é perdida, mas ele será novamente preenchido conforme os usuários restabelecer suas conexões. Armazenamento na memória não funcionará se o seu ambiente incluir mais de um servidor web, pois cada servidor teria uma coleção separada de conexões.

O primeiro exemplo mostra uma classe que gerencia o mapeamento de usuários para conexões. A chave para HashSet será o nome do usuário.

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

O exemplo a seguir mostra como usar a classe de mapeamento de conexão de um hub. A instância da classe é armazenada em um nome de variável `_connections`.

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>Grupos de usuário único

Você pode criar um grupo para cada usuário e, em seguida, enviar uma mensagem a esse grupo quando quiser acessar somente esse usuário. O nome de cada grupo é o nome do usuário. Se um usuário tiver mais de uma conexão, cada id de conexão é adicionada ao grupo do usuário.

Você deve não remover manualmente o usuário a partir do grupo quando o usuário se desconecta. Essa ação é executada automaticamente pela estrutura do SignalR.

O exemplo a seguir mostra como implementar os grupos de usuário único.

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>Armazenamento externo permanente

Este tópico mostra como usar um banco de dados ou o armazenamento de tabelas do Azure para armazenar informações de conexão. Essa abordagem funciona quando você tiver vários servidores web, porque cada servidor web pode interagir com o mesmo repositório de dados. Se seus servidores web parar de funcionar ou o aplicativo ser reiniciado, o `OnDisconnected` método não é chamado. Portanto, é possível que seu repositório de dados tenha registros para ids de conexão que não são mais válidas. Para limpar esses registros órfãos, você poderá invalidar a qualquer conexão que foi criado fora de um período de tempo que é relevante para seu aplicativo. Os exemplos nesta seção incluem um valor para controlar quando a conexão foi criada, mas não mostram como a limpeza dos registros antigos, porque você talvez queira fazer isso como um processo em segundo plano.

### <a name="database"></a>Banco de Dados

Os exemplos a seguir mostram como reter informações de conexão e do usuário em um banco de dados. Você pode usar qualquer tecnologia de acesso a dados; No entanto, o exemplo a seguir mostra como definir modelos usando o Entity Framework. Esses modelos de entidade correspondem aos campos e tabelas de banco de dados. A estrutura de dados pode variar consideravelmente dependendo dos requisitos do seu aplicativo.

O primeiro exemplo mostra como definir uma entidade de usuário que pode ser associada com muitas entidades de conexão.

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

Em seguida, no hub, você pode acompanhar o estado de cada conexão com o código mostrado abaixo.

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

### <a name="azure-table-storage"></a>Armazenamento de tabelas do Azure

O exemplo de armazenamento de tabela do Azure a seguir é semelhante ao exemplo de banco de dados. Ele não inclui todas as informações que você precisa para começar com o serviço de armazenamento de tabela do Azure. Para obter informações, consulte [como usar o armazenamento de tabela do .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

O exemplo a seguir mostra uma entidade de tabela para armazenar informações de conexão. Ela particiona os dados por nome de usuário e identifica cada entidade pela id de conexão, portanto, um usuário pode ter várias conexões a qualquer momento.

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

No hub, você acompanhar o status de conexão de cada usuário.

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]
