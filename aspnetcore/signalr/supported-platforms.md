---
title: ASP.NET Core SignalR plataformas com suporte
author: bradygaster
description: Saiba mais sobre as plataformas com suporte para SignalRde ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9b9cf1d57d61c333c485f23b7ab952c66814d2aa
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317475"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a>ASP.NET Core SignalR plataformas com suporte

## <a name="server-system-requirements"></a>Requisitos de sistema do servidor

SignalR para ASP.NET Core dá suporte a qualquer plataforma de servidor com suporte no ASP.NET Core.

## <a name="javascript-client"></a>Cliente JavaScript

O [cliente JavaScript](xref:signalr/javascript-client) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:

| Navegador                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | &dagger; atual |
| Mozilla Firefox                 | &dagger; atual |
| Google Chrome; inclui Android | &dagger; atual |
| Safari inclui iOS            | &dagger; atual |
| Microsoft Internet Explorer     | 11              |

&dagger;*atual* refere-se à versão mais recente do navegador.

## <a name="net-client"></a>Cliente .NET

O [cliente .net](xref:signalr/dotnet-client) é executado em qualquer plataforma com suporte do ASP.NET Core. Por exemplo, [os desenvolvedores do xamarin podem usar SignalR](https://github.com/aspnet/Announcements/issues/305) para criar aplicativos Android usando o Xamarin. Android 8.4.0.1 e versões posteriores e aplicativos Ios usando o Xamarin. Ios 11.14.0.4 e posterior.

Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior. Outros transportes têm suporte em todas as plataformas.

## <a name="java-client"></a>Cliente Java

O [cliente Java](xref:signalr/java-client) dá suporte a Java 8 e versões posteriores.

## <a name="unsupported-clients"></a>Clientes sem suporte

Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais. Atualmente, eles não têm suporte e podem nunca ser.

* [C++cliente](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)
