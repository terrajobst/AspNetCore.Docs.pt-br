---
title: Gerenciar usuários e grupos no SignalR
author: bradygaster
description: Visão geral do gerenciamento de usuários do SignalR do ASP.NET Core e o grupo.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 0a4836cfa3cf79136b56da1ff05ce8533b4df16c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837878"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="6aae9-103">Gerenciar usuários e grupos no SignalR</span><span class="sxs-lookup"><span data-stu-id="6aae9-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="6aae9-104">Por [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="6aae9-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="6aae9-105">O SignalR permite que mensagens sejam enviadas para todas as conexões associadas a um usuário específico, bem como grupos de conexões nomeados.</span><span class="sxs-lookup"><span data-stu-id="6aae9-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="6aae9-106">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6aae9-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="6aae9-107">Usuários no SignalR</span><span class="sxs-lookup"><span data-stu-id="6aae9-107">Users in SignalR</span></span>

<span data-ttu-id="6aae9-108">O SignalR permite enviar mensagens para todas as conexões associadas a um usuário específico.</span><span class="sxs-lookup"><span data-stu-id="6aae9-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="6aae9-109">Por padrão, o SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado com a conexão como o identificador de usuário.</span><span class="sxs-lookup"><span data-stu-id="6aae9-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="6aae9-110">Um único usuário pode ter várias conexões a um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="6aae9-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="6aae9-111">Por exemplo, um usuário pode ser conectado em sua área de trabalho, bem como seu telefone.</span><span class="sxs-lookup"><span data-stu-id="6aae9-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="6aae9-112">Cada dispositivo tem uma conexão SignalR separado, mas eles são todos associados ao mesmo usuário.</span><span class="sxs-lookup"><span data-stu-id="6aae9-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="6aae9-113">Se uma mensagem é enviada para o usuário, todas as conexões associadas ao usuário recebem a mensagem.</span><span class="sxs-lookup"><span data-stu-id="6aae9-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="6aae9-114">O identificador de usuário para uma conexão pode ser acessado pelo `Context.UserIdentifier` propriedade em seu hub.</span><span class="sxs-lookup"><span data-stu-id="6aae9-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="6aae9-115">Enviar uma mensagem para um usuário específico, passando o identificador de usuário para o `User` funcionar no seu método de hub, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="6aae9-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="6aae9-116">O identificador de usuário diferencia maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6aae9-116">The user identifier is case-sensitive.</span></span>

```csharp
public Task SendPrivateMessage(string user, string message)
{
    return Clients.User(user).SendAsync("ReceiveMessage", message);
}
```

<span data-ttu-id="6aae9-117">O identificador de usuário pode ser personalizado com a criação de um `IUserIdProvider`e registrá-lo no `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6aae9-117">The user identifier can be customized by creating an `IUserIdProvider`, and registering it in `ConfigureServices`.</span></span>

[!code-csharp[UserIdProvider](groups/sample/customuseridprovider.cs?range=4-10)]

[!code-csharp[Configure service](groups/sample/startup.cs?range=21-22,39-42)]

> [!NOTE]
> <span data-ttu-id="6aae9-118">AddSignalR deve ser chamado antes de registrar os serviços SignalR personalizados.</span><span class="sxs-lookup"><span data-stu-id="6aae9-118">AddSignalR must be called before registering your custom SignalR services.</span></span>

## <a name="groups-in-signalr"></a><span data-ttu-id="6aae9-119">Grupos no SignalR</span><span class="sxs-lookup"><span data-stu-id="6aae9-119">Groups in SignalR</span></span>

<span data-ttu-id="6aae9-120">Um grupo é uma coleção de conexões associado com um nome.</span><span class="sxs-lookup"><span data-stu-id="6aae9-120">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="6aae9-121">As mensagens podem ser enviadas para todas as conexões em um grupo.</span><span class="sxs-lookup"><span data-stu-id="6aae9-121">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="6aae9-122">Grupos são a maneira recomendada para enviar para uma conexão ou várias conexões, porque os grupos são gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6aae9-122">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="6aae9-123">Uma conexão pode ser um membro de vários grupos.</span><span class="sxs-lookup"><span data-stu-id="6aae9-123">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="6aae9-124">Isso torna grupos ideal para algo como um aplicativo de bate-papo, onde cada sala pode ser representada como um grupo.</span><span class="sxs-lookup"><span data-stu-id="6aae9-124">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="6aae9-125">Conexões podem ser adicionadas ou removidas de grupos por meio de `AddToGroupAsync` e `RemoveFromGroupAsync` métodos.</span><span class="sxs-lookup"><span data-stu-id="6aae9-125">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="6aae9-126">Associação de grupo não é preservada quando uma conexão se reconecta.</span><span class="sxs-lookup"><span data-stu-id="6aae9-126">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="6aae9-127">A conexão precisa ingressar novamente o grupo quando é restabelecida.</span><span class="sxs-lookup"><span data-stu-id="6aae9-127">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="6aae9-128">Não é possível contar os membros de um grupo, uma vez que essas informações não estarão disponíveis se o aplicativo é dimensionado para vários servidores.</span><span class="sxs-lookup"><span data-stu-id="6aae9-128">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="6aae9-129">Para proteger o acesso a recursos durante o uso de grupos, use [autenticação e autorização](xref:signalr/authn-and-authz) funcionalidade no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6aae9-129">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="6aae9-130">Se você adicionar apenas os usuários a um grupo quando as credenciais são válidas para esse grupo, as mensagens enviadas a esse grupo só irá para os usuários autorizados.</span><span class="sxs-lookup"><span data-stu-id="6aae9-130">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="6aae9-131">No entanto, os grupos não são um recurso de segurança.</span><span class="sxs-lookup"><span data-stu-id="6aae9-131">However, groups are not a security feature.</span></span> <span data-ttu-id="6aae9-132">Declarações de autenticação têm recursos que grupos não fizer isso, como a expiração e a revogação.</span><span class="sxs-lookup"><span data-stu-id="6aae9-132">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="6aae9-133">Se a permissão do usuário para acessar o grupo for revogada, você precisa detectar que e removê-las manualmente.</span><span class="sxs-lookup"><span data-stu-id="6aae9-133">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="6aae9-134">Nomes de grupo diferenciam maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6aae9-134">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="6aae9-135">Recursos relacionados</span><span class="sxs-lookup"><span data-stu-id="6aae9-135">Related resources</span></span>

* [<span data-ttu-id="6aae9-136">Introdução</span><span class="sxs-lookup"><span data-stu-id="6aae9-136">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6aae9-137">Hubs</span><span class="sxs-lookup"><span data-stu-id="6aae9-137">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6aae9-138">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="6aae9-138">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
