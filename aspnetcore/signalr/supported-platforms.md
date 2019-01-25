---
title: Plataformas com suporte do SignalR do ASP.NET Core
author: bradygaster
description: Saiba mais sobre as plataformas com suporte para o SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2018
uid: signalr/supported-platforms
ms.openlocfilehash: e4e84baf0120036b473eac256107b46a4accfe37
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837709"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Plataformas com suporte do SignalR do ASP.NET Core

## <a name="server-system-requirements"></a>Requisitos de sistema do servidor

SignalR para ASP.NET Core dá suporte a qualquer plataforma de servidor que dá suporte ao ASP.NET Core.

## <a name="javascript-client"></a>Cliente JavaScript

O [cliente JavaScript](https://www.npmjs.com/package/@aspnet/signalr) é executado no NodeJS 8 e versões posteriores e os seguintes navegadores:

| Navegador                         | Versão |
| ------------------------------- | ------- |
| Microsoft Edge                  | atual |
| Mozilla Firefox                 | atual |
| Google Chrome; inclui o Android | atual |
| Safari; inclui o iOS            | atual |
| Microsoft Internet Explorer     | 11      |
 
## <a name="net-client"></a>Cliente .NET

O [cliente .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) é executado em qualquer plataforma com suporte pelo ASP.NET Core. Por exemplo, [desenvolvedores Xamarin podem usar o SignalR](https://github.com/aspnet/Announcements/issues/305) para a criação de aplicativos Android usando o xamarin. Android 8.4.0.1 e posterior e aplicativos iOS usando xamarin. IOS 11.14.0.4 e versões posteriores.

Se o servidor executa o IIS, o transporte de WebSockets requer o IIS 8.0 ou superior no Windows Server 2012 ou superior. Outros transportes têm suporte em todas as plataformas.

## <a name="java-client"></a>Cliente de Java

O [cliente Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) dá suporte a Java 8 e versões posteriores.

## <a name="unsupported-clients"></a>Não há suporte para clientes

Os clientes a seguir estão disponíveis, mas são experimentais ou não oficial. Eles não têm suporte no momento e nunca podem ser.

* [Cliente C++](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Cliente SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
