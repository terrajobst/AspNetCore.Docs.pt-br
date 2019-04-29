---
title: Hospedar e implantar o Blazor no servidor
author: guardrex
description: Saiba como hospedar e implantar um aplicativo Blazor do servidor usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 940020ee44d72d50395aad64bc924413c1bbecfb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614618"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hospedar e implantar o Blazor no servidor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

Os aplicativos do servidor que usam o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side-hosting-model) podem aceitar [valores de configuração do Host Genérico](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implantação

Com o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side-hosting-model), o Blazor é executado no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).

O aplicativo é incluído com o aplicativo do ASP.NET Core no resultado publicado e os dois aplicativos são implantados juntos. É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core. Para uma implantação do lado do servidor, o Visual Studio inclui o modelo de projeto **Componentes do Razor** (modelo `razorcomponents` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.

Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.
