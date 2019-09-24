---
title: Hospedar e implantar ASP.NET Core servidor mais novo
author: guardrex
description: Saiba como hospedar e implantar um aplicativo de servidor mais novo usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: aedef7fe695dd4a0cbf04d3f3e9947f33f7afa40
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211611"
---
# <a name="host-and-deploy-blazor-server"></a>Hospedar e implantar um servidor mais novo

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

Os [aplicativos de servidor mais incrivelmente](xref:blazor/hosting-models#blazor-server) podem aceitar valores de configuração de [host genéricos](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implantação

Usando o [modelo de Hospedagem de servidor mais incrivelmente](xref:blazor/hosting-models#blazor-server), o mais incrivelmente é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão [SignalR](xref:signalr/introduction).

É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core. O Visual Studio inclui o modelo de projeto **Aplicativo de Servidor Blazor** (modelo `blazorserverside` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).

## <a name="scalability"></a>Dimensionamento

Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um aplicativo de servidor mais incrivelmente. Consulte os seguintes recursos para abordar a escalabilidade do aplicativo de servidor mais incrivelmente:

* [Conceitos básicos dos aplicativos de servidor mais importantes](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Servidor de implantação

Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem. A memória disponível no servidor afeta o:

* Número de circuitos ativos aos quais um servidor pode dar suporte.
* Latência da interface do usuário no cliente.

Para obter orientação sobre a criação de aplicativos de servidor mais seguros e <xref:security/blazor/server>escalonáveis, consulte.

Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*. O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente. Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: Se você espera que seu aplicativo dê suporte a 5.000 usuários simultâneos, considere o orçamento de pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).

### <a name="signalr-configuration"></a>Configuração do SignalR

Os aplicativos de servidor mais incrivelmente usam ASP.NET Core Signalr para se comunicar com o navegador. As [condições de hospedagem e de colocação do signalr](xref:signalr/publish-to-azure-web-app) aplicam-se a aplicativos de servidor mais incrivelmente.

O mais bem funciona melhor ao usar o WebSocket como o transporte do Signalr devido à menor latência, confiabilidade e [segurança](xref:signalr/security). A sondagem longa é usada pelo Signalr quando o WebSockets não está disponível ou quando o aplicativo é configurado explicitamente para usar a sondagem longa. Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço. Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [diretrizes de publicação do signalr](xref:signalr/publish-to-azure-web-app).

É recomendável usar o [serviço de signalr do Azure](/azure/azure-signalr) para aplicativos de servidor mais incrivelmente. O serviço permite escalar verticalmente um aplicativo de servidor mais recente para um grande número de conexões de Signaler simultâneas. Além disso, o alcance global do serviço Signalr e os data centers de alto desempenho ajudam significativamente na redução da latência devido à geografia.

### <a name="measure-network-latency"></a>Medir latência de rede

A [interoperabilidade js](xref:blazor/javascript-interop) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

Para uma experiência de interface do usuário razoável, recomendamos uma latência de interface do usuário sustentada de 250 MS ou menos.
