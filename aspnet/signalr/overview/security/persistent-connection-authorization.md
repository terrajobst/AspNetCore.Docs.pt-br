---
uid: signalr/overview/security/persistent-connection-authorization
title: Autenticação e autorização para conexões persistentes do SignalR | Microsoft Docs
author: bradygaster
description: Este tópico descreve como impor autorização em uma conexão persistente. Para obter informações gerais sobre como integrar a segurança em um aplicativo do SignalR,...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: e264677b-9c01-47ec-94f9-3cd8f08f94af
msc.legacyurl: /signalr/overview/security/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7dab28f4720b34082f71e487c64af88a8ba01e6c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835616"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections"></a>Autenticação e autorização para conexões persistentes do SignalR
====================
by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tópico descreve como impor autorização em uma conexão persistente. Para obter informações gerais sobre como integrar a segurança em um aplicativo do SignalR, consulte [Introdução à segurança](introduction-to-security.md).
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


## <a name="enforce-authorization"></a>Implantar a autorização

Para impor regras de autorização ao usar um [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) você deve substituir o `AuthorizeRequest` método. Não é possível usar o `Authorize` atributo com conexões persistentes. O `AuthorizeRequest` método é chamado pelo SignalR Framework antes de cada solicitação para verificar se o usuário está autorizado a executar a ação solicitada. O `AuthorizeRequest` método não é chamado do cliente; em vez disso, autenticar o usuário por meio do mecanismo de autenticação padrão do seu aplicativo.

O exemplo a seguir mostra como limitar as solicitações para usuários autenticados.

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

Você pode adicionar qualquer lógica de autorização personalizado no método AuthorizeRequest; Por exemplo, verificando se um usuário pertence a uma função específica.
