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
<a name="mapping-signalr-users-to-connections"></a><span data-ttu-id="1586a-105">Mapeamento de usuários do SignalR para conexões</span><span class="sxs-lookup"><span data-stu-id="1586a-105">Mapping SignalR Users to Connections</span></span>
====================
<span data-ttu-id="1586a-106">por [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="1586a-106">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="1586a-107">Este tópico mostra como reter informações sobre usuários e suas conexões.</span><span class="sxs-lookup"><span data-stu-id="1586a-107">This topic shows how to retain information about users and their connections.</span></span>
>
> <span data-ttu-id="1586a-108">Patrick Fletcher ajudaram a escrever este tópico.</span><span class="sxs-lookup"><span data-stu-id="1586a-108">Patrick Fletcher helped write this topic.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="1586a-109">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="1586a-109">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="1586a-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="1586a-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="1586a-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="1586a-111">.NET 4.5</span></span>
> - <span data-ttu-id="1586a-112">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="1586a-112">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="1586a-113">Versões anteriores deste tópico</span><span class="sxs-lookup"><span data-stu-id="1586a-113">Previous versions of this topic</span></span>
>
> <span data-ttu-id="1586a-114">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="1586a-114">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="1586a-115">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="1586a-115">Questions and comments</span></span>
>
> <span data-ttu-id="1586a-116">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="1586a-116">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="1586a-117">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="1586a-117">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="introduction"></a><span data-ttu-id="1586a-118">Introdução</span><span class="sxs-lookup"><span data-stu-id="1586a-118">Introduction</span></span>

<span data-ttu-id="1586a-119">Cada cliente que se conecta a um hub passa uma id de conexão exclusivo. Você pode recuperar esse valor no `Context.ConnectionId` propriedade de contexto de hub.</span><span class="sxs-lookup"><span data-stu-id="1586a-119">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="1586a-120">Se seu aplicativo precisar mapear um usuário para a id de conexão e manter esse mapeamento, você pode usar um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="1586a-120">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- [<span data-ttu-id="1586a-121">O provedor de ID de usuário (SignalR 2)</span><span class="sxs-lookup"><span data-stu-id="1586a-121">The User ID Provider (SignalR 2)</span></span>](#IUserIdProvider)
- <span data-ttu-id="1586a-122">[Armazenamento na memória](#inmemory), como um dicionário</span><span class="sxs-lookup"><span data-stu-id="1586a-122">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="1586a-123">Grupo do SignalR para cada usuário</span><span class="sxs-lookup"><span data-stu-id="1586a-123">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="1586a-124">[Armazenamento externo permanente](#database), como uma tabela de banco de dados ou o armazenamento de tabelas do Azure</span><span class="sxs-lookup"><span data-stu-id="1586a-124">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="1586a-125">Cada uma dessas implementações é mostrada neste tópico.</span><span class="sxs-lookup"><span data-stu-id="1586a-125">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="1586a-126">Você usa o `OnConnected`, `OnDisconnected`, e `OnReconnected` métodos do `Hub` classe para acompanhar o status de conexão do usuário.</span><span class="sxs-lookup"><span data-stu-id="1586a-126">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="1586a-127">A melhor abordagem para seu aplicativo depende de:</span><span class="sxs-lookup"><span data-stu-id="1586a-127">The best approach for your application depends on:</span></span>

- <span data-ttu-id="1586a-128">O número de servidores web que hospedam seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1586a-128">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="1586a-129">Se você precisa obter uma lista dos usuários conectados no momento.</span><span class="sxs-lookup"><span data-stu-id="1586a-129">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="1586a-130">Se você precisar persistir informações de usuário e grupo quando o aplicativo ou o servidor for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="1586a-130">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="1586a-131">Se a latência da chamada de um servidor externo é um problema.</span><span class="sxs-lookup"><span data-stu-id="1586a-131">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="1586a-132">A tabela a seguir mostra qual abordagem funciona para essas considerações.</span><span class="sxs-lookup"><span data-stu-id="1586a-132">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="1586a-133">Mais de um servidor</span><span class="sxs-lookup"><span data-stu-id="1586a-133">More than one server</span></span> | <span data-ttu-id="1586a-134">Obter lista de usuários conectados no momento</span><span class="sxs-lookup"><span data-stu-id="1586a-134">Get list of currently connected users</span></span> | <span data-ttu-id="1586a-135">Manter as informações depois que for reiniciado</span><span class="sxs-lookup"><span data-stu-id="1586a-135">Persist information after restarts</span></span> | <span data-ttu-id="1586a-136">Desempenho ideal</span><span class="sxs-lookup"><span data-stu-id="1586a-136">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="1586a-137">Provedor de ID de usuário</span><span class="sxs-lookup"><span data-stu-id="1586a-137">UserID Provider</span></span> | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="1586a-138">Na memória</span><span class="sxs-lookup"><span data-stu-id="1586a-138">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="1586a-139">Grupos de usuário único</span><span class="sxs-lookup"><span data-stu-id="1586a-139">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| <span data-ttu-id="1586a-140">Permanentes, externas</span><span class="sxs-lookup"><span data-stu-id="1586a-140">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a><span data-ttu-id="1586a-141">Provedor de IUserID</span><span class="sxs-lookup"><span data-stu-id="1586a-141">IUserID provider</span></span>

<span data-ttu-id="1586a-142">Esse recurso permite aos usuários especificar o que é a ID do usuário com base em um IRequest por meio de uma nova interface IUserIdProvider.</span><span class="sxs-lookup"><span data-stu-id="1586a-142">This feature allows users to specify what the userId is based on an IRequest via a new interface IUserIdProvider.</span></span>

<span data-ttu-id="1586a-143">**O IUserIdProvider**</span><span class="sxs-lookup"><span data-stu-id="1586a-143">**The IUserIdProvider**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="1586a-144">Por padrão, haverá uma implementação que usa o usuário `IPrincipal.Identity.Name` como o nome de usuário.</span><span class="sxs-lookup"><span data-stu-id="1586a-144">By default, there will be an implementation that uses the user's `IPrincipal.Identity.Name` as the user name.</span></span> <span data-ttu-id="1586a-145">Para alterar isso, registre sua implementação de `IUserIdProvider` com o host global quando seu aplicativo é iniciado:</span><span class="sxs-lookup"><span data-stu-id="1586a-145">To change this, register your implementation of `IUserIdProvider` with the global host when your application starts:</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<span data-ttu-id="1586a-146">De dentro de um hub, você poderá enviar mensagens para esses usuários por meio da API a seguir:</span><span class="sxs-lookup"><span data-stu-id="1586a-146">From within a hub, you'll be able to send messages to these users via the following API:</span></span>

<span data-ttu-id="1586a-147">**Enviando uma mensagem para um usuário específico**</span><span class="sxs-lookup"><span data-stu-id="1586a-147">**Sending a message to a specific user**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="1586a-148">Armazenamento na memória</span><span class="sxs-lookup"><span data-stu-id="1586a-148">In-memory storage</span></span>

<span data-ttu-id="1586a-149">Os exemplos a seguir mostram como reter informações de conexão e do usuário em um dicionário que é armazenado na memória.</span><span class="sxs-lookup"><span data-stu-id="1586a-149">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="1586a-150">O dictionary usa um `HashSet` para armazenar a id de conexão. A qualquer momento, um usuário pode ter mais de uma conexão para o aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="1586a-150">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="1586a-151">Por exemplo, um usuário que está conectado por meio de vários dispositivos ou mais de uma guia do navegador teria mais de uma id de conexão.</span><span class="sxs-lookup"><span data-stu-id="1586a-151">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="1586a-152">Se o aplicativo é desligado, todas as informações é perdida, mas ele será novamente preenchido conforme os usuários restabelecer suas conexões.</span><span class="sxs-lookup"><span data-stu-id="1586a-152">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="1586a-153">Armazenamento na memória não funcionará se o seu ambiente incluir mais de um servidor web, pois cada servidor teria uma coleção separada de conexões.</span><span class="sxs-lookup"><span data-stu-id="1586a-153">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="1586a-154">O primeiro exemplo mostra uma classe que gerencia o mapeamento de usuários para conexões.</span><span class="sxs-lookup"><span data-stu-id="1586a-154">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="1586a-155">A chave para HashSet será o nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="1586a-155">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="1586a-156">O exemplo a seguir mostra como usar a classe de mapeamento de conexão de um hub.</span><span class="sxs-lookup"><span data-stu-id="1586a-156">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="1586a-157">A instância da classe é armazenada em um nome de variável `_connections`.</span><span class="sxs-lookup"><span data-stu-id="1586a-157">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="1586a-158">Grupos de usuário único</span><span class="sxs-lookup"><span data-stu-id="1586a-158">Single-user groups</span></span>

<span data-ttu-id="1586a-159">Você pode criar um grupo para cada usuário e, em seguida, enviar uma mensagem a esse grupo quando quiser acessar somente esse usuário.</span><span class="sxs-lookup"><span data-stu-id="1586a-159">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="1586a-160">O nome de cada grupo é o nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="1586a-160">The name of each group is the name of the user.</span></span> <span data-ttu-id="1586a-161">Se um usuário tiver mais de uma conexão, cada id de conexão é adicionada ao grupo do usuário.</span><span class="sxs-lookup"><span data-stu-id="1586a-161">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="1586a-162">Você deve não remover manualmente o usuário a partir do grupo quando o usuário se desconecta.</span><span class="sxs-lookup"><span data-stu-id="1586a-162">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="1586a-163">Essa ação é executada automaticamente pela estrutura do SignalR.</span><span class="sxs-lookup"><span data-stu-id="1586a-163">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="1586a-164">O exemplo a seguir mostra como implementar os grupos de usuário único.</span><span class="sxs-lookup"><span data-stu-id="1586a-164">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="1586a-165">Armazenamento externo permanente</span><span class="sxs-lookup"><span data-stu-id="1586a-165">Permanent, external storage</span></span>

<span data-ttu-id="1586a-166">Este tópico mostra como usar um banco de dados ou o armazenamento de tabelas do Azure para armazenar informações de conexão.</span><span class="sxs-lookup"><span data-stu-id="1586a-166">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="1586a-167">Essa abordagem funciona quando você tiver vários servidores web, porque cada servidor web pode interagir com o mesmo repositório de dados.</span><span class="sxs-lookup"><span data-stu-id="1586a-167">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="1586a-168">Se seus servidores web parar de funcionar ou o aplicativo ser reiniciado, o `OnDisconnected` método não é chamado.</span><span class="sxs-lookup"><span data-stu-id="1586a-168">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="1586a-169">Portanto, é possível que seu repositório de dados tenha registros para ids de conexão que não são mais válidas.</span><span class="sxs-lookup"><span data-stu-id="1586a-169">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="1586a-170">Para limpar esses registros órfãos, você poderá invalidar a qualquer conexão que foi criado fora de um período de tempo que é relevante para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1586a-170">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="1586a-171">Os exemplos nesta seção incluem um valor para controlar quando a conexão foi criada, mas não mostram como a limpeza dos registros antigos, porque você talvez queira fazer isso como um processo em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="1586a-171">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="1586a-172">Banco de Dados</span><span class="sxs-lookup"><span data-stu-id="1586a-172">Database</span></span>

<span data-ttu-id="1586a-173">Os exemplos a seguir mostram como reter informações de conexão e do usuário em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1586a-173">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="1586a-174">Você pode usar qualquer tecnologia de acesso a dados; No entanto, o exemplo a seguir mostra como definir modelos usando o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1586a-174">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="1586a-175">Esses modelos de entidade correspondem aos campos e tabelas de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1586a-175">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="1586a-176">A estrutura de dados pode variar consideravelmente dependendo dos requisitos do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1586a-176">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="1586a-177">O primeiro exemplo mostra como definir uma entidade de usuário que pode ser associada com muitas entidades de conexão.</span><span class="sxs-lookup"><span data-stu-id="1586a-177">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

<span data-ttu-id="1586a-178">Em seguida, no hub, você pode acompanhar o estado de cada conexão com o código mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="1586a-178">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a><span data-ttu-id="1586a-179">Armazenamento de tabelas do Azure</span><span class="sxs-lookup"><span data-stu-id="1586a-179">Azure table storage</span></span>

<span data-ttu-id="1586a-180">O exemplo de armazenamento de tabela do Azure a seguir é semelhante ao exemplo de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1586a-180">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="1586a-181">Ele não inclui todas as informações que você precisa para começar com o serviço de armazenamento de tabela do Azure.</span><span class="sxs-lookup"><span data-stu-id="1586a-181">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="1586a-182">Para obter informações, consulte [como usar o armazenamento de tabela do .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span><span class="sxs-lookup"><span data-stu-id="1586a-182">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="1586a-183">O exemplo a seguir mostra uma entidade de tabela para armazenar informações de conexão.</span><span class="sxs-lookup"><span data-stu-id="1586a-183">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="1586a-184">Ela particiona os dados por nome de usuário e identifica cada entidade pela id de conexão, portanto, um usuário pode ter várias conexões a qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="1586a-184">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

<span data-ttu-id="1586a-185">No hub, você acompanhar o status de conexão de cada usuário.</span><span class="sxs-lookup"><span data-stu-id="1586a-185">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]
