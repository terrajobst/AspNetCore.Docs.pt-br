---
uid: signalr/overview/guide-to-the-api/working-with-groups
title: Trabalhando com grupos no SignalR | Microsoft Docs
author: bradygaster
description: Este tópico descreve como manter as informações de associação de grupo com a API do Hub.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: cd378ecd-3e9e-4236-b902-65916d85a048
msc.legacyurl: /signalr/overview/guide-to-the-api/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: 384b7e5f07fa46ea3cc32e5c18c3c2327b7aedd3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836799"
---
<a name="working-with-groups-in-signalr"></a><span data-ttu-id="26162-103">Trabalhando com grupos no SignalR</span><span class="sxs-lookup"><span data-stu-id="26162-103">Working with Groups in SignalR</span></span>
====================
<span data-ttu-id="26162-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="26162-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="26162-105">Este tópico descreve como adicionar usuários a grupos e manter informações de associação de grupo.</span><span class="sxs-lookup"><span data-stu-id="26162-105">This topic describes how to add users to groups and persist group membership information.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="26162-106">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="26162-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="26162-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="26162-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="26162-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="26162-108">.NET 4.5</span></span>
> - <span data-ttu-id="26162-109">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="26162-109">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="26162-110">Versões anteriores deste tópico</span><span class="sxs-lookup"><span data-stu-id="26162-110">Previous versions of this topic</span></span>
>
> <span data-ttu-id="26162-111">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="26162-111">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="26162-112">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="26162-112">Questions and comments</span></span>
>
> <span data-ttu-id="26162-113">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="26162-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="26162-114">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="26162-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="26162-115">Visão geral</span><span class="sxs-lookup"><span data-stu-id="26162-115">Overview</span></span>

<span data-ttu-id="26162-116">Grupos no SignalR fornecem um método para mensagens de difusão para subconjuntos especificados de clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="26162-116">Groups in SignalR provide a method for broadcasting messages to specified subsets of connected clients.</span></span> <span data-ttu-id="26162-117">Um grupo pode ter qualquer número de clientes e um cliente pode ser um membro de qualquer número de grupos.</span><span class="sxs-lookup"><span data-stu-id="26162-117">A group can have any number of clients, and a client can be a member of any number of groups.</span></span> <span data-ttu-id="26162-118">Você não precisa criar explicitamente grupos.</span><span class="sxs-lookup"><span data-stu-id="26162-118">You don't have to explicitly create groups.</span></span> <span data-ttu-id="26162-119">Na verdade, um grupo é criado automaticamente na primeira vez em que você especifica seu nome em uma chamada para Groups.Add e é excluído quando você remove a última conexão da associação nele.</span><span class="sxs-lookup"><span data-stu-id="26162-119">In effect, a group is automatically created the first time you specify its name in a call to Groups.Add, and it is deleted when you remove the last connection from membership in it.</span></span> <span data-ttu-id="26162-120">Para obter uma introdução ao uso de grupos, consulte [como gerenciar a associação de grupo da classe Hub](hubs-api-guide-server.md#groupsfromhub) na API de Hubs - guia servidor.</span><span class="sxs-lookup"><span data-stu-id="26162-120">For an introduction to using groups, see [How to manage group membership from the Hub class](hubs-api-guide-server.md#groupsfromhub) in the Hubs API - Server Guide.</span></span>

<span data-ttu-id="26162-121">Há uma API para obter uma lista de membros do grupo ou uma lista de grupos.</span><span class="sxs-lookup"><span data-stu-id="26162-121">There is no API for getting a group membership list or a list of groups.</span></span> <span data-ttu-id="26162-122">O SignalR envia mensagens para os clientes e grupos com base em um modelo de pub/sub e o servidor não mantém listas de grupos ou associações de grupo.</span><span class="sxs-lookup"><span data-stu-id="26162-122">SignalR sends messages to clients and groups based on a pub/sub model, and the server does not maintain lists of groups or group memberships.</span></span> <span data-ttu-id="26162-123">Isso ajuda a maximizar a escalabilidade, porque sempre que você adicionar um nó a uma web farm, qualquer estado que mantém o SignalR tem que ser propagadas para o novo nó.</span><span class="sxs-lookup"><span data-stu-id="26162-123">This helps maximize scalability, because whenever you add a node to a web farm, any state that SignalR maintains has to be propagated to the new node.</span></span>

<span data-ttu-id="26162-124">Quando você adiciona um usuário a um grupo usando o `Groups.Add` método, o usuário recebe mensagens direcionadas a esse grupo durante a conexão atual, mas a associação do usuário ao grupo não persiste além a conexão atual.</span><span class="sxs-lookup"><span data-stu-id="26162-124">When you add a user to a group using the `Groups.Add` method, the user receives messages directed to that group for the duration of the current connection, but the user's membership in that group is not persisted beyond the current connection.</span></span> <span data-ttu-id="26162-125">Se você quiser reter permanentemente informações sobre grupos e associação de grupo, você deve armazenar esses dados em um repositório, como um banco de dados ou armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="26162-125">If you want to permanently retain information about groups and group membership, you must store that data in a repository such as a database or Azure table storage.</span></span> <span data-ttu-id="26162-126">Em seguida, sempre que um usuário se conecta ao seu aplicativo, você recuperar do repositório de quais grupos o usuário pertence e manualmente adicione esse usuário aos grupos.</span><span class="sxs-lookup"><span data-stu-id="26162-126">Then, each time a user connects to your application, you retrieve from the repository which groups the user belongs to, and manually add that user to those groups.</span></span>

<span data-ttu-id="26162-127">Quando voltar a ligar após uma interrupção temporária, o usuário novamente associa automaticamente os grupos atribuídos previamente.</span><span class="sxs-lookup"><span data-stu-id="26162-127">When reconnecting after a temporary disruption, the user automatically re-joins the previously-assigned groups.</span></span> <span data-ttu-id="26162-128">Associando automaticamente um grupo se aplica somente ao reconectar, não ao estabelecer uma nova conexão.</span><span class="sxs-lookup"><span data-stu-id="26162-128">Automatically rejoining a group only applies when reconnecting, not when establishing a new connection.</span></span> <span data-ttu-id="26162-129">Um token assinado digitalmente é passado do cliente que contém a lista de grupos atribuídos anteriormente.</span><span class="sxs-lookup"><span data-stu-id="26162-129">A digitally-signed token is passed from the client that contains the list of previously-assigned groups.</span></span> <span data-ttu-id="26162-130">Se você quiser verificar se o usuário pertence aos grupos solicitados, você pode substituir o comportamento padrão.</span><span class="sxs-lookup"><span data-stu-id="26162-130">If you want to verify whether the user belongs to the requested groups, you can override the default behavior.</span></span>

<span data-ttu-id="26162-131">Este tópico inclui as seções a seguir:</span><span class="sxs-lookup"><span data-stu-id="26162-131">This topic includes the following sections:</span></span>

- [<span data-ttu-id="26162-132">Adicionando e removendo usuários</span><span class="sxs-lookup"><span data-stu-id="26162-132">Adding and removing users</span></span>](#add)
- [<span data-ttu-id="26162-133">Chamando membros de um grupo</span><span class="sxs-lookup"><span data-stu-id="26162-133">Calling members of a group</span></span>](#call)
- [<span data-ttu-id="26162-134">Armazenamento de associação de grupo em um banco de dados</span><span class="sxs-lookup"><span data-stu-id="26162-134">Storing group membership in a database</span></span>](#storedatabase)
- [<span data-ttu-id="26162-135">Associação de grupo de armazenamento no armazenamento de tabelas do Azure</span><span class="sxs-lookup"><span data-stu-id="26162-135">Storing group membership in Azure table storage</span></span>](#storeazuretable)
- [<span data-ttu-id="26162-136">Verificar a associação de grupo ao reconectar</span><span class="sxs-lookup"><span data-stu-id="26162-136">Verifying group membership when reconnecting</span></span>](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a><span data-ttu-id="26162-137">Adicionando e removendo usuários</span><span class="sxs-lookup"><span data-stu-id="26162-137">Adding and removing users</span></span>

<span data-ttu-id="26162-138">Para adicionar ou remover usuários de um grupo, você chama o [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) ou [remover](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) métodos e passe o nome do grupo como parâmetros e o id de conexão do usuário.</span><span class="sxs-lookup"><span data-stu-id="26162-138">To add or remove users from a group, you call the [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) or [Remove](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) methods, and pass in the user's connection id and group's name as parameters.</span></span> <span data-ttu-id="26162-139">Não é necessário remover manualmente um usuário de um grupo quando termina a conexão.</span><span class="sxs-lookup"><span data-stu-id="26162-139">You do not need to manually remove a user from a group when the connection ends.</span></span>

<span data-ttu-id="26162-140">A exemplo a seguir mostra a `Groups.Add` e `Groups.Remove` métodos usados em métodos de Hub.</span><span class="sxs-lookup"><span data-stu-id="26162-140">The following example shows the `Groups.Add` and `Groups.Remove` methods used in Hub methods.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

<span data-ttu-id="26162-141">O `Groups.Add` e `Groups.Remove` métodos são executados de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="26162-141">The `Groups.Add` and `Groups.Remove` methods execute asynchronously.</span></span>

<span data-ttu-id="26162-142">Se você quiser adicionar um cliente a um grupo e imediatamente a enviar uma mensagem para o cliente usando o grupo, você precisa certificar-se de que o método Groups.Add termina primeiro.</span><span class="sxs-lookup"><span data-stu-id="26162-142">If you want to add a client to a group and immediately send a message to the client by using the group, you have to make sure that the Groups.Add method finishes first.</span></span> <span data-ttu-id="26162-143">Os exemplos de código a seguir mostram como fazer isso.</span><span class="sxs-lookup"><span data-stu-id="26162-143">The following code examples show how to do that.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

<span data-ttu-id="26162-144">Em geral, você não deve incluir `await` ao chamar o `Groups.Remove` método como a id de conexão que você está tentando remover pode não estar disponível.</span><span class="sxs-lookup"><span data-stu-id="26162-144">In general, you should not include `await` when calling the `Groups.Remove` method because the connection id that you are trying to remove might no longer be available.</span></span> <span data-ttu-id="26162-145">Nesse caso, `TaskCanceledException` for gerada depois que a solicitação expira. Se seu aplicativo deve garantir que o usuário foi removido do grupo antes de enviar uma mensagem para o grupo, você pode adicionar `await` antes de `Groups.Remove`e, em seguida, capture a `TaskCanceledException` exceção que pode ser gerada.</span><span class="sxs-lookup"><span data-stu-id="26162-145">In that case, `TaskCanceledException` is thrown after the request times out. If your application must ensure that the user has been removed from the group before sending a message to the group, you can add `await` before `Groups.Remove`, and then catch the `TaskCanceledException` exception that might be thrown.</span></span>

<a id="call"></a>

## <a name="calling-members-of-a-group"></a><span data-ttu-id="26162-146">Chamando membros de um grupo</span><span class="sxs-lookup"><span data-stu-id="26162-146">Calling members of a group</span></span>

<span data-ttu-id="26162-147">Você pode enviar mensagens para todos os membros de um grupo ou somente os membros especificados do grupo, conforme mostrado nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="26162-147">You can send messages to all of the members of a group or only specified members of the group, as shown in the following examples.</span></span>

- <span data-ttu-id="26162-148">**Todos os** conectados a clientes em um grupo especificado.</span><span class="sxs-lookup"><span data-stu-id="26162-148">**All** connected clients in a specified group.</span></span>

    [!code-css[Main](working-with-groups/samples/sample3.css)]
- <span data-ttu-id="26162-149">Todos os clientes em um grupo especificado conectados **clientes especificados, exceto**, identificado pela ID de conexão.</span><span class="sxs-lookup"><span data-stu-id="26162-149">All connected clients in a specified group **except the specified clients**, identified by connection ID.</span></span>

    [!code-csharp[Main](working-with-groups/samples/sample4.cs)]
- <span data-ttu-id="26162-150">Todos os clientes em um grupo especificado conectados **exceto o cliente da chamada**.</span><span class="sxs-lookup"><span data-stu-id="26162-150">All connected clients in a specified group **except the calling client**.</span></span>

    [!code-css[Main](working-with-groups/samples/sample5.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a><span data-ttu-id="26162-151">Armazenamento de associação de grupo em um banco de dados</span><span class="sxs-lookup"><span data-stu-id="26162-151">Storing group membership in a database</span></span>

<span data-ttu-id="26162-152">Os exemplos a seguir mostram como reter informações de usuário e grupo em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="26162-152">The following examples show how to retain group and user information in a database.</span></span> <span data-ttu-id="26162-153">Você pode usar qualquer tecnologia de acesso a dados; No entanto, o exemplo a seguir mostra como definir modelos usando o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="26162-153">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="26162-154">Esses modelos de entidade correspondem aos campos e tabelas de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="26162-154">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="26162-155">A estrutura de dados pode variar consideravelmente dependendo dos requisitos do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="26162-155">Your data structure could vary considerably depending on the requirements of your application.</span></span> <span data-ttu-id="26162-156">Este exemplo inclui uma classe chamada `ConversationRoom` que seria exclusiva de um aplicativo que permite que os usuários ingressem conversas sobre assuntos diferentes, como o ambiente ou de esportes.</span><span class="sxs-lookup"><span data-stu-id="26162-156">This example includes a class named `ConversationRoom` which would be unique to an application that enables users to join conversations about different subjects, such as sports or gardening.</span></span> <span data-ttu-id="26162-157">Este exemplo também inclui uma classe para as conexões.</span><span class="sxs-lookup"><span data-stu-id="26162-157">This example also includes a class for the connections.</span></span> <span data-ttu-id="26162-158">A classe de conexão não é absolutamente necessária para rastrear a associação de grupo, mas com frequência faz parte de uma solução robusta para acompanhamento de usuários.</span><span class="sxs-lookup"><span data-stu-id="26162-158">The connection class is not absolutely required for tracking group membership but is frequently part of robust solution to tracking users.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample6.cs)]

<span data-ttu-id="26162-159">Em seguida, no hub, você pode recuperar as informações de grupo e usuário do banco de dados e adicionar manualmente o usuário aos grupos apropriados.</span><span class="sxs-lookup"><span data-stu-id="26162-159">Then, in the hub, you can retrieve the group and user information from the database and manually add the user to the appropriate groups.</span></span> <span data-ttu-id="26162-160">O exemplo inclui código para acompanhar as conexões de usuário.</span><span class="sxs-lookup"><span data-stu-id="26162-160">The example does not include code for tracking the user connections.</span></span> <span data-ttu-id="26162-161">Neste exemplo, o `await` palavra-chave não é aplicada antes `Groups.Add` porque uma mensagem não será enviada imediatamente para membros do grupo.</span><span class="sxs-lookup"><span data-stu-id="26162-161">In this example, the `await` keyword is not applied before `Groups.Add` because a message is not immediately sent to members of the group.</span></span> <span data-ttu-id="26162-162">Se você quiser enviar uma mensagem a todos os membros do grupo logo depois de adicionar o novo membro, convém aplicar o `await` palavra-chave para garantir que a operação assíncrona foi concluída.</span><span class="sxs-lookup"><span data-stu-id="26162-162">If you want to send a message to all members of the group immediately after adding the new member, you would want to apply the `await` keyword to make sure the asynchronous operation has completed.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a><span data-ttu-id="26162-163">Associação de grupo de armazenamento no armazenamento de tabelas do Azure</span><span class="sxs-lookup"><span data-stu-id="26162-163">Storing group membership in Azure table storage</span></span>

<span data-ttu-id="26162-164">Usando o armazenamento de tabela do Azure para armazenar informações de usuário e grupo é semelhante ao uso de um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="26162-164">Using Azure table storage to store group and user information is similar to using a database.</span></span> <span data-ttu-id="26162-165">O exemplo a seguir mostra uma entidade de tabela que armazena o nome de usuário e o nome do grupo.</span><span class="sxs-lookup"><span data-stu-id="26162-165">The following example shows a table entity that stores the user name and group name.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

<span data-ttu-id="26162-166">No hub, você recuperar os grupos atribuídos quando o usuário se conecta.</span><span class="sxs-lookup"><span data-stu-id="26162-166">In the hub, you retrieve the assigned groups when the user connects.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a><span data-ttu-id="26162-167">Verificar a associação de grupo ao reconectar</span><span class="sxs-lookup"><span data-stu-id="26162-167">Verifying group membership when reconnecting</span></span>

<span data-ttu-id="26162-168">Por padrão, o SignalR novamente atribui automaticamente um usuário aos grupos apropriados ao reconectar-se de uma interrupção temporária, como quando uma conexão será descartada e restabelecida antes que a conexão expire. Informações de grupo do usuário são passadas em um token ao reconectar e esse token é verificado no servidor.</span><span class="sxs-lookup"><span data-stu-id="26162-168">By default, SignalR automatically re-assigns a user to the appropriate groups when reconnecting from a temporary disruption, such as when a connection is dropped and re-established before the connection times out. The user's group information is passed in a token when reconnecting, and that token is verified on the server.</span></span> <span data-ttu-id="26162-169">Para obter informações sobre o processo de verificação para incluir novamente grupos de usuários, consulte [incluir novamente grupos ao reconectar](../security/introduction-to-security.md#rejoingroup).</span><span class="sxs-lookup"><span data-stu-id="26162-169">For information about the verification process for rejoining users to groups, see [Rejoining groups when reconnecting](../security/introduction-to-security.md#rejoingroup).</span></span>

<span data-ttu-id="26162-170">Em geral, você deve usar o comportamento padrão do automaticamente incluir novamente grupos na reconexão.</span><span class="sxs-lookup"><span data-stu-id="26162-170">In general, you should use the default behavior of automatically rejoining groups on reconnect.</span></span> <span data-ttu-id="26162-171">Grupos de SignalR não foram projetados como um mecanismo de segurança para restringir o acesso a dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="26162-171">SignalR groups are not intended as a security mechanism for restricting access to sensitive data.</span></span> <span data-ttu-id="26162-172">No entanto, se seu aplicativo precisa verificar a associação de grupo do usuário ao reconectar, você pode substituir o comportamento padrão.</span><span class="sxs-lookup"><span data-stu-id="26162-172">However, if your application must double-check a user's group membership when reconnecting, you can override the default behavior.</span></span> <span data-ttu-id="26162-173">Alterando o comportamento padrão pode adicionar uma carga para seu banco de dados porque a associação de grupo do usuário deve ser recuperada para cada reconexão em vez de apenas quando o usuário se conecta.</span><span class="sxs-lookup"><span data-stu-id="26162-173">Changing the default behavior can add a burden to your database because a user's group membership must be retrieved for each reconnection rather than just when the user connects.</span></span>

<span data-ttu-id="26162-174">Se você precisar verificar associação de grupo em reconectar, crie um novo módulo de pipeline de hub que retorna uma lista de grupos atribuídos, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="26162-174">If you must verify group membership on reconnect, create a new hub pipeline module that returns a list of assigned groups, as shown below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

<span data-ttu-id="26162-175">Em seguida, adicione o módulo para o pipeline de hub, como destacado abaixo.</span><span class="sxs-lookup"><span data-stu-id="26162-175">Then, add that module to the hub pipeline, as highlighted below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample11.cs?highlight=4)]
