---
title: Recursos de cliente SignalR
author: bradygaster
description: Saiba quais recursos são compatíveis com os vários clientes do Signalr ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301183"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.NET Core recursos de cliente do Signalr

## <a name="feature-distribution"></a>Distribuição de recursos

A tabela a seguir mostra os recursos e o suporte para os clientes que oferecem suporte em tempo real.

| Recurso | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Suporte ao serviço de Signaler do Azure |✔|✔|✔|
| [Streaming de servidor para cliente](xref:signalr/streaming)          |✔|✔|✔|
| [Streaming de cliente para servidor](xref:signalr/streaming)          |✔|✔|✔|
| Reconexão automática ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |✔|✔| |
| Transporte de WebSockets |✔|✔|✔|
| Transporte de eventos enviados pelo servidor |✔|✔| |
| Transporte de sondagem longa |✔|✔|✔|
| Protocolo de Hub JSON |✔|✔|✔|
| Protocolo de Hub do MessagePack |✔|✔| |

O suporte para reconexão automática no cliente Java é acompanhado em [nosso Issue Tracker](https://github.com/aspnet/AspNetCore/issues/8711).

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução ao Signalr para ASP.NET Core](xref:tutorials/signalr)
* [Plataformas compatíveis](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
