---
uid: signalr/overview/guide-to-the-api/mapping-users-to-connections
title: Mapeamento de usuários do SignalR para conexões | Microsoft Docs
author: Rick-Anderson
description: Este tópico mostra como reter informações sobre usuários e suas conexões. Patrick Fletcher ajudaram a escrever este tópico. Versões de software usadas neste tópico...
ms.author: riande
ms.date: 12/30/2014
ms.assetid: f80c08b1-3f1f-432c-980c-c7b6edeb31b1
msc.legacyurl: /signalr/overview/guide-to-the-api/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: f24d3a4828bc0863f8213e53f054aa7914edffad
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287750"
---
<a name="mapping-signalr-users-to-connections"></a>Mapeamento de usuários do SignalR para conexões
====================
por [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tópico mostra como reter informações sobre usuários e suas conexões.
>
> Patrick Fletcher ajudaram a escrever este tópico.
>
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

## <a name="introduction"></a>Introdução

Cada cliente que se conecta a um hub passa uma id de conexão exclusivo. Você pode recuperar esse valor no `Context.ConnectionId` propriedade de contexto de hub. Se seu aplicativo precisar mapear um usuário para a id de conexão e manter esse mapeamento, você pode usar um dos seguintes:

- [O provedor de ID de usuário (SignalR 2)](#IUserIdProvider)
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
| Provedor de ID de usuário | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| Na memória |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| Grupos de usuário único | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| Permanentes, externas | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a>Provedor de IUserID

Esse recurso permite aos usuários especificar o que é a ID do usuário com base em um IRequest por meio de uma nova interface IUserIdProvider.

**O IUserIdProvider**

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

Por padrão, haverá uma implementação que usa o usuário `IPrincipal.Identity.Name` como o nome de usuário. Para alterar isso, registre sua implementação de `IUserIdProvider` com o host global quando seu aplicativo é iniciado:

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

De dentro de um hub, você poderá enviar mensagens para esses usuários por meio da API a seguir:

**Enviando uma mensagem para um usuário específico**

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>Armazenamento na memória

Os exemplos a seguir mostram como reter informações de conexão e do usuário em um dicionário que é armazenado na memória. O dictionary usa um `HashSet` para armazenar a id de conexão. A qualquer momento, um usuário pode ter mais de uma conexão para o aplicativo do SignalR. Por exemplo, um usuário que está conectado por meio de vários dispositivos ou mais de uma guia do navegador teria mais de uma id de conexão.

Se o aplicativo é desligado, todas as informações é perdida, mas ele será novamente preenchido conforme os usuários restabelecer suas conexões. Armazenamento na memória não funcionará se o seu ambiente incluir mais de um servidor web, pois cada servidor teria uma coleção separada de conexões.

O primeiro exemplo mostra uma classe que gerencia o mapeamento de usuários para conexões. A chave para HashSet será o nome do usuário.

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

O exemplo a seguir mostra como usar a classe de mapeamento de conexão de um hub. A instância da classe é armazenada em um nome de variável `_connections`.

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>Grupos de usuário único

Você pode criar um grupo para cada usuário e, em seguida, enviar uma mensagem a esse grupo quando quiser acessar somente esse usuário. O nome de cada grupo é o nome do usuário. Se um usuário tiver mais de uma conexão, cada id de conexão é adicionada ao grupo do usuário.

Você deve não remover manualmente o usuário a partir do grupo quando o usuário se desconecta. Essa ação é executada automaticamente pela estrutura do SignalR.

O exemplo a seguir mostra como implementar os grupos de usuário único.

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>Armazenamento externo permanente

Este tópico mostra como usar um banco de dados ou o armazenamento de tabelas do Azure para armazenar informações de conexão. Essa abordagem funciona quando você tiver vários servidores web, porque cada servidor web pode interagir com o mesmo repositório de dados. Se seus servidores web parar de funcionar ou o aplicativo ser reiniciado, o `OnDisconnected` método não é chamado. Portanto, é possível que seu repositório de dados tenha registros para ids de conexão que não são mais válidas. Para limpar esses registros órfãos, você poderá invalidar a qualquer conexão que foi criado fora de um período de tempo que é relevante para seu aplicativo. Os exemplos nesta seção incluem um valor para controlar quando a conexão foi criada, mas não mostram como a limpeza dos registros antigos, porque você talvez queira fazer isso como um processo em segundo plano.

### <a name="database"></a>Banco de Dados

Os exemplos a seguir mostram como reter informações de conexão e do usuário em um banco de dados. Você pode usar qualquer tecnologia de acesso a dados; No entanto, o exemplo a seguir mostra como definir modelos usando o Entity Framework. Esses modelos de entidade correspondem aos campos e tabelas de banco de dados. A estrutura de dados pode variar consideravelmente dependendo dos requisitos do seu aplicativo.

O primeiro exemplo mostra como definir uma entidade de usuário que pode ser associada com muitas entidades de conexão.

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

Em seguida, no hub, você pode acompanhar o estado de cada conexão com o código mostrado abaixo.

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a>Armazenamento de tabelas do Azure

O exemplo de armazenamento de tabela do Azure a seguir é semelhante ao exemplo de banco de dados. Ele não inclui todas as informações que você precisa para começar com o serviço de armazenamento de tabela do Azure. Para obter informações, consulte [como usar o armazenamento de tabela do .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).

O exemplo a seguir mostra uma entidade de tabela para armazenar informações de conexão. Ela particiona os dados por nome de usuário e identifica cada entidade pela id de conexão, portanto, um usuário pode ter várias conexões a qualquer momento.

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

No hub, você acompanhar o status de conexão de cada usuário.

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]
