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
# <a name="manage-users-and-groups-in-signalr"></a>Gerenciar usuários e grupos no SignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

O SignalR permite que mensagens sejam enviadas para todas as conexões associadas a um usuário específico, bem como grupos de conexões nomeados.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Usuários no SignalR

O SignalR permite enviar mensagens para todas as conexões associadas a um usuário específico. Por padrão, o SignalR usa o `ClaimTypes.NameIdentifier` do `ClaimsPrincipal` associado com a conexão como o identificador de usuário. Um único usuário pode ter várias conexões a um aplicativo do SignalR. Por exemplo, um usuário pode ser conectado em sua área de trabalho, bem como seu telefone. Cada dispositivo tem uma conexão SignalR separado, mas eles são todos associados ao mesmo usuário. Se uma mensagem é enviada para o usuário, todas as conexões associadas ao usuário recebem a mensagem. O identificador de usuário para uma conexão pode ser acessado pelo `Context.UserIdentifier` propriedade em seu hub.

Enviar uma mensagem para um usuário específico, passando o identificador de usuário para o `User` funcionar no seu método de hub, conforme mostrado no exemplo a seguir:

> [!NOTE]
> O identificador de usuário diferencia maiusculas de minúsculas.

```csharp
public Task SendPrivateMessage(string user, string message)
{
    return Clients.User(user).SendAsync("ReceiveMessage", message);
}
```

O identificador de usuário pode ser personalizado com a criação de um `IUserIdProvider`e registrá-lo no `ConfigureServices`.

[!code-csharp[UserIdProvider](groups/sample/customuseridprovider.cs?range=4-10)]

[!code-csharp[Configure service](groups/sample/startup.cs?range=21-22,39-42)]

> [!NOTE]
> AddSignalR deve ser chamado antes de registrar os serviços SignalR personalizados.

## <a name="groups-in-signalr"></a>Grupos no SignalR

Um grupo é uma coleção de conexões associado com um nome. As mensagens podem ser enviadas para todas as conexões em um grupo. Grupos são a maneira recomendada para enviar para uma conexão ou várias conexões, porque os grupos são gerenciados pelo aplicativo. Uma conexão pode ser um membro de vários grupos. Isso torna grupos ideal para algo como um aplicativo de bate-papo, onde cada sala pode ser representada como um grupo. Conexões podem ser adicionadas ou removidas de grupos por meio de `AddToGroupAsync` e `RemoveFromGroupAsync` métodos.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

Associação de grupo não é preservada quando uma conexão se reconecta. A conexão precisa ingressar novamente o grupo quando é restabelecida. Não é possível contar os membros de um grupo, uma vez que essas informações não estarão disponíveis se o aplicativo é dimensionado para vários servidores.

Para proteger o acesso a recursos durante o uso de grupos, use [autenticação e autorização](xref:signalr/authn-and-authz) funcionalidade no ASP.NET Core. Se você adicionar apenas os usuários a um grupo quando as credenciais são válidas para esse grupo, as mensagens enviadas a esse grupo só irá para os usuários autorizados. No entanto, os grupos não são um recurso de segurança. Declarações de autenticação têm recursos que grupos não fizer isso, como a expiração e a revogação. Se a permissão do usuário para acessar o grupo for revogada, você precisa detectar que e removê-las manualmente.

> [!NOTE]
> Nomes de grupo diferenciam maiusculas de minúsculas.

## <a name="related-resources"></a>Recursos relacionados

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
