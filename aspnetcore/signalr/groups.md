---
title: Gerenciar usuários e grupos no SignalR
author: bradygaster
description: Visão geral do gerenciamento de usuários do SignalR do ASP.NET Core e o grupo.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 45f2bb44e03a586b7fc186525fdd3a2645c820d5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667746"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="5021e-103">Gerenciar usuários e grupos no SignalR</span><span class="sxs-lookup"><span data-stu-id="5021e-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="5021e-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="5021e-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="5021e-105">O SignalR permite que mensagens sejam enviadas para todas as conexões associadas a um usuário específico, bem como grupos de conexões nomeados.</span><span class="sxs-lookup"><span data-stu-id="5021e-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="5021e-106">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5021e-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="5021e-107">Usuários no SignalR</span><span class="sxs-lookup"><span data-stu-id="5021e-107">Users in SignalR</span></span>

<span data-ttu-id="5021e-108">O SignalR permite enviar mensagens para todas as conexões associadas a um usuário específico.</span><span class="sxs-lookup"><span data-stu-id="5021e-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="5021e-109">Por padrão, o SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado com a conexão como o identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="5021e-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="5021e-110">Um único usuário pode ter várias conexões a um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="5021e-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="5021e-111">Por exemplo, um usuário pode ser conectado em sua área de trabalho, bem como seu telefone.</span><span class="sxs-lookup"><span data-stu-id="5021e-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="5021e-112">Cada dispositivo tem uma conexão SignalR separado, mas eles são todos associados ao mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="5021e-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="5021e-113">Se uma mensagem é enviada para o usuário, todas as conexões associadas ao usuário recebem a mensagem.</span><span class="sxs-lookup"><span data-stu-id="5021e-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="5021e-114">O identificador de usuário para uma conexão pode ser acessado pelo `Context.UserIdentifier` propriedade em seu hub.</span><span class="sxs-lookup"><span data-stu-id="5021e-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="5021e-115">Enviar uma mensagem para um usuário específico, passando o identificador de usuário para o `User` funcionar no seu método de hub, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5021e-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="5021e-116">O identificador de usuário diferencia maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5021e-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="5021e-117">Grupos no SignalR</span><span class="sxs-lookup"><span data-stu-id="5021e-117">Groups in SignalR</span></span>

<span data-ttu-id="5021e-118">Um grupo é uma coleção de conexões associado com um nome.</span><span class="sxs-lookup"><span data-stu-id="5021e-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="5021e-119">As mensagens podem ser enviadas para todas as conexões em um grupo.</span><span class="sxs-lookup"><span data-stu-id="5021e-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="5021e-120">Grupos são a maneira recomendada para enviar para uma conexão ou várias conexões, porque os grupos são gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5021e-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="5021e-121">Uma conexão pode ser um membro de vários grupos.</span><span class="sxs-lookup"><span data-stu-id="5021e-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="5021e-122">Isso torna grupos ideal para algo como um aplicativo de bate-papo, onde cada sala pode ser representada como um grupo.</span><span class="sxs-lookup"><span data-stu-id="5021e-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="5021e-123">Conexões podem ser adicionadas ou removidas de grupos por meio de `AddToGroupAsync` e `RemoveFromGroupAsync` métodos.</span><span class="sxs-lookup"><span data-stu-id="5021e-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="5021e-124">Associação de grupo não é preservada quando uma conexão se reconecta.</span><span class="sxs-lookup"><span data-stu-id="5021e-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="5021e-125">A conexão precisa ingressar novamente o grupo quando é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="5021e-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="5021e-126">Não é possível contar os membros de um grupo, uma vez que essas informações não estarão disponíveis se o aplicativo é dimensionado para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="5021e-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="5021e-127">Para proteger o acesso a recursos durante o uso de grupos, use [autenticação e autorização](xref:signalr/authn-and-authz) funcionalidade no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5021e-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="5021e-128">Se você adicionar apenas os usuários a um grupo quando as credenciais são válidas para esse grupo, as mensagens enviadas a esse grupo só irá para os usuários autorizados.</span><span class="sxs-lookup"><span data-stu-id="5021e-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="5021e-129">No entanto, os grupos não são um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="5021e-129">However, groups are not a security feature.</span></span> <span data-ttu-id="5021e-130">Declarações de autenticação têm recursos que grupos não fizer isso, como a expiração e a revogação.</span><span class="sxs-lookup"><span data-stu-id="5021e-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="5021e-131">Se a permissão do usuário para acessar o grupo for revogada, você precisa detectar que e removê-las manualmente.</span><span class="sxs-lookup"><span data-stu-id="5021e-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="5021e-132">Nomes de grupo diferenciam maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5021e-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="5021e-133">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="5021e-133">Related resources</span></span>

* [<span data-ttu-id="5021e-134">Introdução</span><span class="sxs-lookup"><span data-stu-id="5021e-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5021e-135">Hubs</span><span class="sxs-lookup"><span data-stu-id="5021e-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5021e-136">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="5021e-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
