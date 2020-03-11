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
ms.openlocfilehash: 7e8c85dcbc46daa68988374f499f19a9d2cead47
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665861"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a>Gerenciar usuários e grupos no SignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

SignalR permite que as mensagens sejam enviadas a todas as conexões associadas a um usuário específico, bem como a grupos nomeados de conexões.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="users-in-opno-locsignalr"></a>Usuários no SignalR

SignalR permite que você envie mensagens para todas as conexões associadas a um usuário específico. Por padrão, SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado à conexão como o identificador de usuário. Um único usuário pode ter várias conexões com um aplicativo SignalR. Por exemplo, um usuário pode estar conectado à área de trabalho e ao seu telefone. Cada dispositivo tem uma conexão SignalR separada, mas todos estão associados ao mesmo usuário. Se uma mensagem for enviada ao usuário, todas as conexões associadas a esse usuário receberão a mensagem. O identificador de usuário para uma conexão pode ser acessado pela propriedade `Context.UserIdentifier` em seu hub.

Envie uma mensagem para um usuário específico passando o identificador de usuário para a função `User` em seu método de Hub, conforme mostrado no exemplo a seguir:

> [!NOTE]
> O identificador de usuário diferencia maiúsculas de minúsculas.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a>Grupos no SignalR

Um grupo é uma coleção de conexões associadas a um nome. As mensagens podem ser enviadas a todas as conexões em um grupo. Os grupos são a maneira recomendada de enviar para uma conexão ou várias conexões, pois os grupos são gerenciados pelo aplicativo. Uma conexão pode ser membro de vários grupos. Isso torna os grupos ideais para algo como um aplicativo de chat, onde cada sala pode ser representada como um grupo. As conexões podem ser adicionadas ou removidas dos grupos por meio dos métodos `AddToGroupAsync` e `RemoveFromGroupAsync`.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

A associação de grupo não é preservada quando uma conexão é reconectada. A conexão precisa reingressar no grupo quando ele for restabelecido. Não é possível contar os membros de um grupo, pois essas informações não estarão disponíveis se o aplicativo for dimensionado para vários servidores.

Para proteger o acesso aos recursos usando grupos, use a funcionalidade de [autenticação e autorização](xref:signalr/authn-and-authz) no ASP.NET Core. Se você apenas adicionar usuários a um grupo quando as credenciais forem válidas para esse grupo, as mensagens enviadas para esse grupo irão apenas para usuários autorizados. No entanto, os grupos não são um recurso de segurança. As declarações de autenticação têm recursos que os grupos não têm, como expiração e revogação. Se a permissão de um usuário para acessar o grupo for revogada, você precisará detectá-lo manualmente e removê-lo do grupo.

> [!NOTE]
> Os nomes de grupo diferenciam maiúsculas de minúsculas.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
