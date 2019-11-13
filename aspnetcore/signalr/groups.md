---
title: Gerenciar usuários e grupos no SignalR
author: bradygaster
description: Visão geral de ASP.NET Core SignalR gerenciamento de usuário e grupo.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/groups
ms.openlocfilehash: 59e90042ecbaf936602643bbdc3965e036426b26
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963817"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a><span data-ttu-id="48197-103">Gerenciar usuários e grupos no SignalR</span><span class="sxs-lookup"><span data-stu-id="48197-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="48197-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="48197-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="48197-105"> permite que as mensagens sejam enviadas a todas as conexões associadas a um usuário específico, bem como a grupos nomeados de conexões.</span><span class="sxs-lookup"><span data-stu-id="48197-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="48197-106">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como baixar)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="48197-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-opno-locsignalr"></a><span data-ttu-id="48197-107">Usuários no SignalR</span><span class="sxs-lookup"><span data-stu-id="48197-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="48197-108"> permite que você envie mensagens para todas as conexões associadas a um usuário específico.</span><span class="sxs-lookup"><span data-stu-id="48197-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="48197-109">Por padrão, SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado à conexão como o identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="48197-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="48197-110">Um único usuário pode ter várias conexões com um aplicativo SignalR.</span><span class="sxs-lookup"><span data-stu-id="48197-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="48197-111">Por exemplo, um usuário pode estar conectado à área de trabalho e ao seu telefone.</span><span class="sxs-lookup"><span data-stu-id="48197-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="48197-112">Cada dispositivo tem uma conexão SignalR separada, mas todos estão associados ao mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="48197-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="48197-113">Se uma mensagem for enviada ao usuário, todas as conexões associadas a esse usuário receberão a mensagem.</span><span class="sxs-lookup"><span data-stu-id="48197-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="48197-114">O identificador de usuário para uma conexão pode ser acessado pela propriedade `Context.UserIdentifier` em seu hub.</span><span class="sxs-lookup"><span data-stu-id="48197-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="48197-115">Envie uma mensagem para um usuário específico passando o identificador de usuário para a função `User` em seu método de Hub, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="48197-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="48197-116">O identificador de usuário diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="48197-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a><span data-ttu-id="48197-117">Grupos no SignalR</span><span class="sxs-lookup"><span data-stu-id="48197-117">Groups in SignalR</span></span>

<span data-ttu-id="48197-118">Um grupo é uma coleção de conexões associadas a um nome.</span><span class="sxs-lookup"><span data-stu-id="48197-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="48197-119">As mensagens podem ser enviadas a todas as conexões em um grupo.</span><span class="sxs-lookup"><span data-stu-id="48197-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="48197-120">Os grupos são a maneira recomendada de enviar para uma conexão ou várias conexões, pois os grupos são gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="48197-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="48197-121">Uma conexão pode ser membro de vários grupos.</span><span class="sxs-lookup"><span data-stu-id="48197-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="48197-122">Isso torna os grupos ideais para algo como um aplicativo de chat, onde cada sala pode ser representada como um grupo.</span><span class="sxs-lookup"><span data-stu-id="48197-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="48197-123">As conexões podem ser adicionadas ou removidas dos grupos por meio dos métodos `AddToGroupAsync` e `RemoveFromGroupAsync`.</span><span class="sxs-lookup"><span data-stu-id="48197-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="48197-124">A associação de grupo não é preservada quando uma conexão é reconectada.</span><span class="sxs-lookup"><span data-stu-id="48197-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="48197-125">A conexão precisa reingressar no grupo quando ele for restabelecido.</span><span class="sxs-lookup"><span data-stu-id="48197-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="48197-126">Não é possível contar os membros de um grupo, pois essas informações não estarão disponíveis se o aplicativo for dimensionado para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="48197-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="48197-127">Para proteger o acesso aos recursos usando grupos, use a funcionalidade de [autenticação e autorização](xref:signalr/authn-and-authz) no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="48197-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="48197-128">Se você apenas adicionar usuários a um grupo quando as credenciais forem válidas para esse grupo, as mensagens enviadas para esse grupo irão apenas para usuários autorizados.</span><span class="sxs-lookup"><span data-stu-id="48197-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="48197-129">No entanto, os grupos não são um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="48197-129">However, groups are not a security feature.</span></span> <span data-ttu-id="48197-130">As declarações de autenticação têm recursos que os grupos não têm, como expiração e revogação.</span><span class="sxs-lookup"><span data-stu-id="48197-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="48197-131">Se a permissão de um usuário para acessar o grupo for revogada, você precisará detectá-lo manualmente e removê-lo do grupo.</span><span class="sxs-lookup"><span data-stu-id="48197-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="48197-132">Os nomes de grupo diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="48197-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="48197-133">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="48197-133">Related resources</span></span>

* [<span data-ttu-id="48197-134">Introdução</span><span class="sxs-lookup"><span data-stu-id="48197-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="48197-135">Hubs</span><span class="sxs-lookup"><span data-stu-id="48197-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="48197-136">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="48197-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
