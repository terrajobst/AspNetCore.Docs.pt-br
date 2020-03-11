---
title: SignalR de ASP.NET Core do host em serviços em segundo plano
author: bradygaster
description: Saiba como enviar mensagens para clientes SignalR de classes BackgroundService do .NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 86319cc93febab18c29e2fb6366cef0d025943ba
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658140"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a>SignalR de ASP.NET Core do host em serviços em segundo plano

Por [Brady GASTER](https://twitter.com/bradygaster)

Este artigo fornece diretrizes para:

* Hospedagem de hubs de SignalR usando um processo de trabalho em segundo plano hospedado com ASP.NET Core.
* Envio de mensagens para clientes conectados de dentro de um [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)do .NET Core.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(como baixar)](xref:index#how-to-download-a-sample)

## <a name="enable-opno-locsignalr-in-startup"></a>Habilitar SignalR na inicialização

::: moniker range=">= aspnetcore-3.0"

Hospedar ASP.NET Core hubs de SignalR no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core. No método `Startup.ConfigureServices`, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência ASP.NET Core (DI) para dar suporte ao SignalR. No `Startup.Configure`, o método `MapHub` é chamado no retorno de chamada `UseEndpoints` para conectar os pontos de extremidade do Hub no pipeline de solicitação do ASP.NET Core.

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

Hospedar ASP.NET Core hubs de SignalR no contexto de um processo de trabalho em segundo plano é idêntico a hospedar um Hub em um aplicativo Web ASP.NET Core. No método `Startup.ConfigureServices`, chamar `services.AddSignalR` adiciona os serviços necessários à camada de injeção de dependência ASP.NET Core (DI) para dar suporte ao SignalR. No `Startup.Configure`, o método `UseSignalR` é chamado para conectar os pontos de extremidade do Hub no pipeline de solicitação ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

No exemplo anterior, a classe `ClockHub` implementa a classe `Hub<T>` para criar um hub fortemente tipado. A `ClockHub` foi configurada na classe `Startup` para responder às solicitações no ponto de extremidade `/hubs/clock`.

Para obter mais informações sobre hubs com rigidez de tipos, consulte [usar hubs em SignalR para ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Essa funcionalidade não é limitada ao [Hub\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1) classe. Qualquer classe que herda do [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), também funcionará.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

A interface usada pelo `ClockHub` com rigidez de tipos é a interface `IClock`.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a>Chamar um hub de SignalR de um serviço em segundo plano

Durante a inicialização, a classe `Worker`, uma `BackgroundService`, é habilitada usando `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Como o SignalR também é habilitado durante a fase de `Startup`, na qual cada Hub é anexado a um ponto de extremidade individual no pipeline de solicitação HTTP de ASP.NET Core, cada Hub é representado por um `IHubContext<T>` no servidor. Usando os recursos de DI do ASP.NET Core, outras classes instanciadas pela camada de hospedagem, como classes de `BackgroundService`, classes do controlador MVC ou modelos de página do Razor, podem obter referências a hubs do lado do servidor aceitando instâncias de `IHubContext<ClockHub, IClock>` durante a construção.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Como o método `ExecuteAsync` é chamado iterativamente no serviço em segundo plano, a data e hora atuais do servidor são enviadas para os clientes conectados usando o `ClockHub`.

## <a name="react-to-opno-locsignalr-events-with-background-services"></a>Reagir a SignalR eventos com serviços em segundo plano

Como um aplicativo de página única usando o cliente JavaScript para SignalR ou um aplicativo de área de trabalho .NET pode usar o <xref:signalr/dotnet-client>, uma implementação `BackgroundService` ou `IHostedService` também pode ser usada para se conectar a hubs de SignalR e responder a eventos.

A classe `ClockHubClient` implementa a interface `IClock` e a interface `IHostedService`. Dessa forma, ele pode ser habilitado durante a `Startup` para ser executado continuamente e responder a eventos de Hub do servidor.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante a inicialização, o `ClockHubClient` cria uma instância de um `HubConnection` e habilita o método `IClock.ShowTime` como o manipulador para o evento de `ShowTime` do Hub.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

Na implementação de `IHostedService.StartAsync`, o `HubConnection` é iniciado de forma assíncrona.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante o método `IHostedService.StopAsync`, a `HubConnection` é descartada de forma assíncrona.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Hubs com rigidez de tipos](xref:signalr/hubs#strongly-typed-hubs)
