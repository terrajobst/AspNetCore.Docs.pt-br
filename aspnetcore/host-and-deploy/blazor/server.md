---
title: Hospedar e implantar ASP.NET Core Blazor Server
author: guardrex
description: Saiba como hospedar e implantar um aplicativo do Blazor Server usando o ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: 3ca4a2235a8129def9e480d0a6ad54cc856920ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025422"
---
# <a name="host-and-deploy-opno-locblazor-server"></a>Hospedar e implantar o servidor de Blazor

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valores de configuração do host

os [aplicativos doBlazor Server](xref:blazor/hosting-models#blazor-server) podem aceitar [valores de configuração de host genéricos](xref:fundamentals/host/generic-host#host-configuration).

## <a name="deployment"></a>Implantação

Usando o [modelo de hospedagem do servidorBlazor](xref:blazor/hosting-models#blazor-server), Blazor é executado no servidor de dentro de um aplicativo ASP.NET Core. As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma conexão [SignalR](xref:signalr/introduction) .

É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core. O Visual Studio inclui o modelo de projeto de **aplicativo doBlazor Server** (`blazorserverside` modelo ao usar o comando [dotnet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Escalabilidade

Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um aplicativo do Blazor Server. Consulte os seguintes recursos para resolver a escalabilidade do aplicativo Blazor Server:

* [Conceitos básicos de aplicativos do Blazor Server](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Servidor de implantação

Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem. A memória disponível no servidor afeta o:

* Número de circuitos ativos aos quais um servidor pode dar suporte.
* Latência da interface do usuário no cliente.

Para obter orientação sobre a criação de aplicativos de servidor Blazor de segurança e escalonáveis, consulte <xref:security/blazor/server>.

Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*. O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente. Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: se você espera que seu aplicativo ofereça suporte a 5.000 usuários simultâneos, considere o orçamento para menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).

### <a name="opno-locsignalr-configuration"></a>configuração de SignalR

os aplicativos do Blazor Server usam ASP.NET Core SignalR para se comunicar com o navegador. as [condições de hospedagem e colocação deSignalR](xref:signalr/publish-to-azure-web-app) se aplicam aos aplicativos Blazor Server.

Blazor funciona melhor ao usar Websockets como transporte de SignalR devido a latência, confiabilidade e [segurança](xref:signalr/security)menores. A sondagem longa é usada pelo SignalR quando o WebSockets não está disponível ou quando o aplicativo é configurado explicitamente para usar a sondagem longa. Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço. Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [diretrizes de publicação deSignalR](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-opno-locsignalr-service"></a>Serviço de SignalR do Azure

É recomendável usar o [serviço de SignalR do Azure](/azure/azure-signalr) para aplicativos do Blazor Server. O serviço permite escalar verticalmente um aplicativo do Blazor Server para um grande número de conexões de SignalR simultâneas. Além disso, o alcance global do serviço de SignalR e os data centers de alto desempenho auxiliam significativamente na redução da latência devido à geografia. Para configurar um aplicativo (e, opcionalmente, provisionar) o serviço de SignalR do Azure:

1. Habilite o serviço para dar suporte a *sessões adesivas*, em que os clientes são [redirecionados de volta para o mesmo servidor ao renderizar](xref:blazor/hosting-models#connection-to-the-server). Defina a opção `ServerStickyMode` ou o valor de configuração como `Required`. Normalmente, um aplicativo cria a configuração usando **uma** das seguintes abordagens:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configuração (use **uma** das seguintes abordagens):
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * A **configuração** do serviço de aplicativo > **configurações do aplicativo** na portal do Azure (**nome**: `Azure:SignalR:ServerStickyMode`, **valor**: `Required`).

1. Crie um perfil de publicação de aplicativos do Azure no Visual Studio para o aplicativo Blazor Server.
1. Adicione a dependência do **serviço de SignalR do Azure** ao perfil. Se a assinatura do Azure não tiver uma instância de serviço de SignalR do Azure já existente para atribuir ao aplicativo, selecione **criar uma nova instância do serviço de SignalR do Azure** para provisionar uma nova instância de serviço.
1. Publicar o aplicativo no Azure.

#### <a name="iis"></a>IIS

Ao usar o IIS, habilite:

* [WebSockets no IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessões adesivas com Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Crie uma definição de entrada com as seguintes [anotações de kubernetes para sessões adesivas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>Linux com o Nginx

Para que SignalR WebSockets funcionem corretamente, confirme se os cabeçalhos `Upgrade` e `Connection` do proxy estão definidos com os valores a seguir e se `$connection_upgrade` está mapeado para:

* O valor do cabeçalho de atualização por padrão.
* `close` quando o cabeçalho de atualização está ausente ou vazio.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Para obter mais informações, consulte os seguintes artigos:

* [NGINX como um proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Medir latência de rede

A [interoperabilidade js](xref:blazor/call-javascript-from-dotnet) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:

```razor
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
