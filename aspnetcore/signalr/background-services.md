---
title: Sinalizador de ASP.NET Core de host em serviços em segundo plano
author: bradygaster
description: Saiba como enviar mensagens para clientes do Signalr de classes BackgroundService do .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: 23a53f33a03ce3b76cf6846c3f214a6cad055209
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773947"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>Sinalizador de ASP.NET Core de host em serviços em segundo plano

Por [Brady GASTER](https://twitter.com/bradygaster)

Este artigo fornece diretrizes para:

* Hospedagem de hubs de sinalização usando um processo de trabalho em segundo plano hospedado com ASP.NET Core.
* Envio de mensagens para clientes conectados de dentro de um [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)do .NET Core.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>Conectar o Signalr durante a inicialização

::: moniker range=">= aspnetcore-3.0"

Hospedar ASP.NET Core hubs de sinalização no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core. No método, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para dar suporte ao signalr. `Startup.ConfigureServices` No `Startup.Configure`, o `MapHub` métodoéchamadonoretornodechamadaparaconectarospontosdeextremidadedoHubnopipelinedesolicitaçãodeASP.NETCore.`UseEndpoints`

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Hospedar ASP.NET Core hubs de sinalização no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core. No método, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência de ASP.NET Core (di) para dar suporte ao signalr. `Startup.ConfigureServices` No `Startup.Configure`, o `UseSignalR` método é chamado para conectar os pontos de extremidade do Hub no pipeline de solicitação ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

No exemplo anterior, a `ClockHub` classe implementa a `Hub<T>` classe para criar um hub fortemente tipado. O `ClockHub` foi configurado `Startup` na classe para responder às solicitações no ponto de extremidade `/hubs/clock`.

Para obter mais informações sobre hubs com rigidez de tipos, consulte [usar hubs no signalr para ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Essa funcionalidade não está limitada à [classe\<t > do Hub](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Qualquer classe que herda do [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), também funcionará.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

A interface usada pelo fortemente tipado `ClockHub` é a `IClock` interface.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Chamar um Hub do Signalr de um serviço em segundo plano

Durante a inicialização, `Worker` a classe `BackgroundService`, a, é conectada `AddHostedService`usando.

```csharp
services.AddHostedService<Worker>();
```

Como o signalr também é conectado durante a `Startup` fase, na qual cada Hub é anexado a um ponto de extremidade individual no pipeline de solicitação HTTP de ASP.NET Core, cada Hub é `IHubContext<T>` representado por um no servidor. Usando os recursos de di do ASP.NET Core, outras classes instanciadas pela camada de hospedagem `BackgroundService` , como classes, classes do controlador MVC ou modelos de página do Razor, podem obter referências a hubs do lado do `IHubContext<ClockHub, IClock>` servidor aceitando instâncias do durante a construção.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Como o `ExecuteAsync` método é chamado iterativamente no serviço em segundo plano, a data e hora atuais do servidor são enviadas para os clientes conectados usando `ClockHub`o.

## <a name="react-to-signalr-events-with-background-services"></a>Reagir a eventos de sinalização com serviços em segundo plano

Como um aplicativo de página única que usa o cliente JavaScript para signalr ou um aplicativo de área de trabalho .net pode <xref:signalr/dotnet-client>fazer uso `BackgroundService` do `IHostedService` , a ou a implementação também pode ser usada para se conectar a hubs de sinalização e responder a eventos.

A `ClockHubClient` classe implementa a `IClock` interface e a `IHostedService` interface. Dessa forma, ele pode ser conectado durante `Startup` a execução contínua e responder a eventos de Hub do servidor.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante a inicialização, `ClockHubClient` o cria uma instância `HubConnection` de a e conecta o `IClock.ShowTime` método como o manipulador para o evento do `ShowTime` Hub.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Na implementação, o é `HubConnection` iniciado de forma assíncrona. `IHostedService.StartAsync`

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante o `IHostedService.StopAsync` método, o `HubConnection` é Descartado de forma assíncrona.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Hubs com rigidez de tipos](xref:signalr/hubs#strongly-typed-hubs)
