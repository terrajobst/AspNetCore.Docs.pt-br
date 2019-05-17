---
title: Hospedar e implantar o Blazor no servidor
author: guardrex
description: Saiba como hospedar e implantar um aplicativo Blazor do servidor usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/26/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8e44be09a4cceba2509f3e86abf3ce5fd2d077bd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887761"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hospedar e implantar o Blazor no servidor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

Os aplicativos do servidor que usam o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side) podem aceitar [valores de configuração do Host Genérico](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implantação

Com o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side), o Blazor é executado no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).

É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core. O Visual Studio inclui o modelo de projeto **Blazor (lado do servidor)** (modelo `blazorserverside` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Implantar a versão de visualização do ASP.NET Core ao Serviço de Aplicativo do Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
