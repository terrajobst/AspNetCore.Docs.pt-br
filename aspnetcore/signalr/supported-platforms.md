---
title: Plataformas com suporte do Signalr ASP.NET Core
author: bradygaster
description: Saiba mais sobre as plataformas com suporte para o Signalr ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426971"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Plataformas com suporte do Signalr ASP.NET Core

## <a name="server-system-requirements"></a>Requisitos de sistema do servidor

O signalr para ASP.NET Core dá suporte a qualquer plataforma de servidor com suporte ASP.NET Core.

## <a name="javascript-client"></a>Cliente JavaScript

O [cliente JavaScript](https://www.npmjs.com/package/@aspnet/signalr) é executado no NodeJS 8 e versões posteriores e nos seguintes navegadores:

| Navegador                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | &dagger; atual |
| Mozilla Firefox                 | &dagger; atual |
| Google Chrome; inclui Android | &dagger; atual |
| Safari inclui iOS            | &dagger; atual |
| Microsoft Internet Explorer     | 11              |

&dagger;*atual* refere-se à versão mais recente do navegador.

## <a name="net-client"></a>Cliente .NET

O [cliente .net](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) é executado em qualquer plataforma com suporte do ASP.NET Core. Por exemplo, [os desenvolvedores do xamarin podem usar o signalr](https://github.com/aspnet/Announcements/issues/305) para criar aplicativos Android usando Xamarin. Android 8.4.0.1 e posterior e aplicativos Ios usando Xamarin. Ios 11.14.0.4 e posterior.

Se o servidor executar o IIS, o transporte do WebSockets exigirá o IIS 8,0 ou posterior no Windows Server 2012 ou posterior. Outros transportes têm suporte em todas as plataformas.

## <a name="java-client"></a>Cliente Java

O [cliente Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) dá suporte a Java 8 e versões posteriores.

## <a name="unsupported-clients"></a>Clientes sem suporte

Os clientes a seguir estão disponíveis, mas são experimentais ou não oficiais. Atualmente, eles não têm suporte e podem nunca ser.

* [C++cliente](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Cliente Swift](https://github.com/moozzyk/SignalR-Client-Swift)
