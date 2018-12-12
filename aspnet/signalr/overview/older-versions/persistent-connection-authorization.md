---
uid: signalr/overview/older-versions/persistent-connection-authorization
title: Autenticação e autorização para conexões persistentes do SignalR (SignalR 1.x) | Microsoft Docs
author: pfletcher
description: Este tópico descreve como impor autorização em uma conexão persistente. Para obter informações gerais sobre como integrar a segurança em um aplicativo do SignalR,...
ms.author: riande
ms.date: 10/21/2013
ms.assetid: c34bc627-41af-4c21-a817-e97a19a7f252
msc.legacyurl: /signalr/overview/older-versions/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 4973cb9bd03088106fe9502e0e706c6c28d2d46d
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286851"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections-signalr-1x"></a>Autenticação e autorização para conexões persistentes do SignalR (SignalR 1.x)
====================
por [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tópico descreve como impor autorização em uma conexão persistente. Para obter informações gerais sobre como integrar a segurança em um aplicativo do SignalR, consulte [Introdução à segurança](index.md).


## <a name="enforce-authorization"></a>Implantar a autorização

Para impor regras de autorização ao usar um [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) você deve substituir o `AuthorizeRequest` método. Não é possível usar o `Authorize` atributo com conexões persistentes. O `AuthorizeRequest` método é chamado pelo SignalR Framework antes de cada solicitação para verificar se o usuário está autorizado a executar a ação solicitada. O `AuthorizeRequest` método não é chamado do cliente; em vez disso, autenticar o usuário por meio do mecanismo de autenticação padrão do seu aplicativo.

O exemplo a seguir mostra como limitar as solicitações para usuários autenticados.

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

Você pode adicionar qualquer lógica de autorização personalizado no método AuthorizeRequest; Por exemplo, verificando se um usuário pertence a uma função específica.
