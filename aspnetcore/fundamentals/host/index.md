---
title: Host da Web e Host Genérico no ASP.NET Core
author: guardrex
description: Saiba mais sobre o Host da Web do ASP.NET Core e o Host Genérico do .NET, que são responsáveis pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/28/2018
uid: fundamentals/host/index
ms.openlocfilehash: 43a68f67368ce37a1fb4032579c6c4e4c05d2719
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284572"
---
# <a name="web-host-and-generic-host-in-aspnet-core"></a>Host da Web e Host Genérico no ASP.NET Core

Aplicativos ASP.NET Core configuram e inicializam um *host*. O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo. Duas APIs de host estão disponíveis para uso:

* [Host da Web](xref:fundamentals/host/web-host) &ndash; Adequado para a hospedagem de aplicativos Web.
* [Host Genérico](xref:fundamentals/host/generic-host) (ASP.NET Core 2.1 ou posteriores) &ndash; Adequado para a hospedagem de aplicativos não Web (por exemplo, aplicativos que executam tarefas em segundo plano). Em uma versão futura, o Host Genérico será adequado para hospedar qualquer tipo de aplicativo, incluindo aplicativos Web. Eventualmente, o Host Genérico substituirá o Host da Web.

Para hospedar *aplicativos Web* do ASP.NET Core, os desenvolvedores devem usar o Web Host com base em <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>. Para hospedar *aplicativos que não sejam Web*, os desenvolvedores devem usar o Host Genérico com base em <xref:Microsoft.Extensions.Hosting.HostBuilder>.

<xref:fundamentals/host/hosted-services>  
Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.

<xref:fundamentals/configuration/platform-specific-configuration>  
Descubra como aprimorar um aplicativo ASP.NET Core por meio de um assembly referenciado ou não referenciado usando uma implementação de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>.
