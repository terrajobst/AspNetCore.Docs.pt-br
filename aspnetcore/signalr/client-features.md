---
title: Recursos de cliente SignalR
author: bradygaster
description: Saiba quais recursos são compatíveis com os vários clientes do Signalr ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925353"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.NET Core recursos de cliente do Signalr

## <a name="feature-distribution"></a>Distribuição de recursos

A tabela a seguir mostra os recursos e o suporte para os clientes que oferecem suporte em tempo real. Para cada recurso, a versão *mínima* que dá suporte a esse recurso está listada. Se nenhuma versão estiver listada, o recurso não terá suporte.

| Recurso | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Suporte ao serviço de Signaler do Azure |1.0.0|1.0.0|1.0.0|
| [Streaming de servidor para cliente](xref:signalr/streaming)          |1.0.0|1.0.0|1.0.0|
| [Streaming de cliente para servidor](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|
| Reconexão automática ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|❌|
| Transporte de WebSockets |1.0.0|1.0.0|1.0.0|
| Transporte de eventos enviados pelo servidor |1.0.0|1.0.0|❌|
| Transporte de sondagem longa |1.0.0|1.0.0|3.0.0|
| Protocolo de Hub JSON |1.0.0|1.0.0|1.0.0|
| Protocolo de Hub do MessagePack |1.0.0|1.0.0|❌|

O suporte para reconexão automática no cliente Java é acompanhado em [nosso Issue Tracker](https://github.com/aspnet/AspNetCore/issues/8711).

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao Signalr para ASP.NET Core](xref:tutorials/signalr)
* [Plataformas compatíveis](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
