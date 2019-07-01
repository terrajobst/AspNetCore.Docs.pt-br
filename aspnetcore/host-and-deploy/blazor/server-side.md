---
title: Hospedar e implantar o ASP.NET Core Blazor no lado do servidor
author: guardrex
description: Saiba como hospedar e implantar um aplicativo Blazor do servidor usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8b332c2fb439e9832d604ed26f972b266eed2507
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406115"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hospedar e implantar o Blazor no servidor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

Os aplicativos do servidor que usam o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side) podem aceitar [valores de configuração do Host Genérico](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implantação

Com o [modelo de hospedagem do servidor](xref:blazor/hosting-models#server-side), o Blazor é executado no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).

É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core. O Visual Studio inclui o modelo de projeto **Blazor (lado do servidor)** (modelo `blazorserverside` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).

## <a name="connection-scale-out"></a>Expandir a conexão

Os aplicativos Blazor do lado do servidor exigem uma conexão ativa do SignalR para cada usuário. Uma implantação do lado do servidor de produção do Blazor requer uma solução para dar suporte a várias conexões simultâneas solicitadas pelo aplicativo. O [Serviço do Azure SignalR](/azure/azure-signalr/) lida com o dimensionamento de conexões e é recomendado como uma solução de dimensionamento para aplicativos Blazor do lado do servidor. Para obter mais informações, consulte <xref:signalr/publish-to-azure-web-app>.

## <a name="signalr-configuration"></a>Configuração do SignalR

O SignalR é configurado pelo ASP.NET Core para os cenários Blazor mais comuns do lado do servidor. Para cenários avançados e personalizados, confira os artigos de SignalR na seção [Recursos adicionais](#additional-resources).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:signalr/introduction>
* [Documentação do Serviço do Azure SignalR](/azure/azure-signalr/)
* [Início Rápido: Criar uma sala de chat usando o Serviço do SignalR](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Implantar a versão de visualização do ASP.NET Core ao Serviço de Aplicativo do Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
