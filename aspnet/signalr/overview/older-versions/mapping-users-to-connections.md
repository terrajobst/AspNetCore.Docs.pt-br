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
<a name="mapping-signalr-users-to-connections-in-signalr-1x"></a><span data-ttu-id="e7a42-103">Mapeamento de usuários do SignalR para conexões no SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="e7a42-103">Mapping SignalR Users to Connections in SignalR 1.x</span></span>
====================
<span data-ttu-id="e7a42-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e7a42-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="e7a42-105">Este tópico mostra como reter informações sobre usuários e suas conexões.</span><span class="sxs-lookup"><span data-stu-id="e7a42-105">This topic shows how to retain information about users and their connections.</span></span>


## <a name="introduction"></a><span data-ttu-id="e7a42-106">Introdução</span><span class="sxs-lookup"><span data-stu-id="e7a42-106">Introduction</span></span>

<span data-ttu-id="e7a42-107">Cada cliente que se conecta a um hub passa uma id de conexão exclusivo. Você pode recuperar esse valor no `Context.ConnectionId` propriedade de contexto de hub.</span><span class="sxs-lookup"><span data-stu-id="e7a42-107">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="e7a42-108">Se seu aplicativo precisar mapear um usuário para a id de conexão e manter esse mapeamento, você pode usar um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="e7a42-108">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- <span data-ttu-id="e7a42-109">[Armazenamento na memória](#inmemory), como um dicionário</span><span class="sxs-lookup"><span data-stu-id="e7a42-109">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="e7a42-110">Grupo do SignalR para cada usuário</span><span class="sxs-lookup"><span data-stu-id="e7a42-110">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="e7a42-111">[Armazenamento externo permanente](#database), como uma tabela de banco de dados ou o armazenamento de tabelas do Azure</span><span class="sxs-lookup"><span data-stu-id="e7a42-111">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="e7a42-112">Cada uma dessas implementações é mostrada neste tópico.</span><span class="sxs-lookup"><span data-stu-id="e7a42-112">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="e7a42-113">Você usa o `OnConnected`, `OnDisconnected`, e `OnReconnected` métodos do `Hub` classe para acompanhar o status de conexão do usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a42-113">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="e7a42-114">A melhor abordagem para seu aplicativo depende de:</span><span class="sxs-lookup"><span data-stu-id="e7a42-114">The best approach for your application depends on:</span></span>

- <span data-ttu-id="e7a42-115">O número de servidores web que hospedam seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7a42-115">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="e7a42-116">Se você precisa obter uma lista dos usuários conectados no momento.</span><span class="sxs-lookup"><span data-stu-id="e7a42-116">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="e7a42-117">Se você precisar persistir informações de usuário e grupo quando o aplicativo ou o servidor for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="e7a42-117">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="e7a42-118">Se a latência da chamada de um servidor externo é um problema.</span><span class="sxs-lookup"><span data-stu-id="e7a42-118">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="e7a42-119">A tabela a seguir mostra qual abordagem funciona para essas considerações.</span><span class="sxs-lookup"><span data-stu-id="e7a42-119">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="e7a42-120">Mais de um servidor</span><span class="sxs-lookup"><span data-stu-id="e7a42-120">More than one server</span></span> | <span data-ttu-id="e7a42-121">Obter lista de usuários conectados no momento</span><span class="sxs-lookup"><span data-stu-id="e7a42-121">Get list of currently connected users</span></span> | <span data-ttu-id="e7a42-122">Manter as informações depois que for reiniciado</span><span class="sxs-lookup"><span data-stu-id="e7a42-122">Persist information after restarts</span></span> | <span data-ttu-id="e7a42-123">Desempenho ideal</span><span class="sxs-lookup"><span data-stu-id="e7a42-123">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="e7a42-124">Na memória</span><span class="sxs-lookup"><span data-stu-id="e7a42-124">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image1.png) |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="e7a42-125">Grupos de usuário único</span><span class="sxs-lookup"><span data-stu-id="e7a42-125">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image3.png) |  |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="e7a42-126">Permanentes, externas</span><span class="sxs-lookup"><span data-stu-id="e7a42-126">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image5.png) | ![](mapping-users-to-connections/_static/image6.png) | ![](mapping-users-to-connections/_static/image7.png) |  |

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="e7a42-127">Armazenamento na memória</span><span class="sxs-lookup"><span data-stu-id="e7a42-127">In-memory storage</span></span>

<span data-ttu-id="e7a42-128">Os exemplos a seguir mostram como reter informações de conexão e do usuário em um dicionário que é armazenado na memória.</span><span class="sxs-lookup"><span data-stu-id="e7a42-128">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="e7a42-129">O dictionary usa um `HashSet` para armazenar a id de conexão. A qualquer momento, um usuário pode ter mais de uma conexão para o aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="e7a42-129">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="e7a42-130">Por exemplo, um usuário que está conectado por meio de vários dispositivos ou mais de uma guia do navegador teria mais de uma id de conexão.</span><span class="sxs-lookup"><span data-stu-id="e7a42-130">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="e7a42-131">Se o aplicativo é desligado, todas as informações é perdida, mas ele será novamente preenchido conforme os usuários restabelecer suas conexões.</span><span class="sxs-lookup"><span data-stu-id="e7a42-131">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="e7a42-132">Armazenamento na memória não funcionará se o seu ambiente incluir mais de um servidor web, pois cada servidor teria uma coleção separada de conexões.</span><span class="sxs-lookup"><span data-stu-id="e7a42-132">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="e7a42-133">O primeiro exemplo mostra uma classe que gerencia o mapeamento de usuários para conexões.</span><span class="sxs-lookup"><span data-stu-id="e7a42-133">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="e7a42-134">A chave para HashSet será o nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a42-134">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="e7a42-135">O exemplo a seguir mostra como usar a classe de mapeamento de conexão de um hub.</span><span class="sxs-lookup"><span data-stu-id="e7a42-135">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="e7a42-136">A instância da classe é armazenada em um nome de variável `_connections`.</span><span class="sxs-lookup"><span data-stu-id="e7a42-136">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="e7a42-137">Grupos de usuário único</span><span class="sxs-lookup"><span data-stu-id="e7a42-137">Single-user groups</span></span>

<span data-ttu-id="e7a42-138">Você pode criar um grupo para cada usuário e, em seguida, enviar uma mensagem a esse grupo quando quiser acessar somente esse usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a42-138">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="e7a42-139">O nome de cada grupo é o nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a42-139">The name of each group is the name of the user.</span></span> <span data-ttu-id="e7a42-140">Se um usuário tiver mais de uma conexão, cada id de conexão é adicionada ao grupo do usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a42-140">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="e7a42-141">Você deve não remover manualmente o usuário a partir do grupo quando o usuário se desconecta.</span><span class="sxs-lookup"><span data-stu-id="e7a42-141">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="e7a42-142">Essa ação é executada automaticamente pela estrutura do SignalR.</span><span class="sxs-lookup"><span data-stu-id="e7a42-142">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="e7a42-143">O exemplo a seguir mostra como implementar os grupos de usuário único.</span><span class="sxs-lookup"><span data-stu-id="e7a42-143">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="e7a42-144">Armazenamento externo permanente</span><span class="sxs-lookup"><span data-stu-id="e7a42-144">Permanent, external storage</span></span>

<span data-ttu-id="e7a42-145">Este tópico mostra como usar um banco de dados ou o armazenamento de tabelas do Azure para armazenar informações de conexão.</span><span class="sxs-lookup"><span data-stu-id="e7a42-145">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="e7a42-146">Essa abordagem funciona quando você tiver vários servidores web, porque cada servidor web pode interagir com o mesmo repositório de dados.</span><span class="sxs-lookup"><span data-stu-id="e7a42-146">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="e7a42-147">Se seus servidores web parar de funcionar ou o aplicativo ser reiniciado, o `OnDisconnected` método não é chamado.</span><span class="sxs-lookup"><span data-stu-id="e7a42-147">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="e7a42-148">Portanto, é possível que seu repositório de dados tenha registros para ids de conexão que não são mais válidas.</span><span class="sxs-lookup"><span data-stu-id="e7a42-148">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="e7a42-149">Para limpar esses registros órfãos, você poderá invalidar a qualquer conexão que foi criado fora de um período de tempo que é relevante para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7a42-149">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="e7a42-150">Os exemplos nesta seção incluem um valor para controlar quando a conexão foi criada, mas não mostram como a limpeza dos registros antigos, porque você talvez queira fazer isso como um processo em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="e7a42-150">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="e7a42-151">Banco de Dados</span><span class="sxs-lookup"><span data-stu-id="e7a42-151">Database</span></span>

<span data-ttu-id="e7a42-152">Os exemplos a seguir mostram como reter informações de conexão e do usuário em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7a42-152">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="e7a42-153">Você pode usar qualquer tecnologia de acesso a dados; No entanto, o exemplo a seguir mostra como definir modelos usando o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e7a42-153">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="e7a42-154">Esses modelos de entidade correspondem aos campos e tabelas de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7a42-154">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="e7a42-155">A estrutura de dados pode variar consideravelmente dependendo dos requisitos do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e7a42-155">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="e7a42-156">O primeiro exemplo mostra como definir uma entidade de usuário que pode ser associada com muitas entidades de conexão.</span><span class="sxs-lookup"><span data-stu-id="e7a42-156">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="e7a42-157">Em seguida, no hub, você pode acompanhar o estado de cada conexão com o código mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="e7a42-157">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

### <a name="azure-table-storage"></a><span data-ttu-id="e7a42-158">Armazenamento de tabelas do Azure</span><span class="sxs-lookup"><span data-stu-id="e7a42-158">Azure table storage</span></span>

<span data-ttu-id="e7a42-159">O exemplo de armazenamento de tabela do Azure a seguir é semelhante ao exemplo de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e7a42-159">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="e7a42-160">Ele não inclui todas as informações que você precisa para começar com o serviço de armazenamento de tabela do Azure.</span><span class="sxs-lookup"><span data-stu-id="e7a42-160">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="e7a42-161">Para obter informações, consulte [como usar o armazenamento de tabela do .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span><span class="sxs-lookup"><span data-stu-id="e7a42-161">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="e7a42-162">O exemplo a seguir mostra uma entidade de tabela para armazenar informações de conexão.</span><span class="sxs-lookup"><span data-stu-id="e7a42-162">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="e7a42-163">Ela particiona os dados por nome de usuário e identifica cada entidade pela id de conexão, portanto, um usuário pode ter várias conexões a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="e7a42-163">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<span data-ttu-id="e7a42-164">No hub, você acompanhar o status de conexão de cada usuário.</span><span class="sxs-lookup"><span data-stu-id="e7a42-164">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]
