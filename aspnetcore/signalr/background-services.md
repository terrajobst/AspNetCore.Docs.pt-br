---
title: Host SignalR do ASP.NET Core em serviços em segundo plano
author: bradygaster
description: Saiba como enviar mensagens para os clientes do SignalR de classes do .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: dcd62f0c7056a3f987291b6c8bb8b87f94160865
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087743"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>Host SignalR do ASP.NET Core em serviços em segundo plano

Por [Brady Gaster](https://twitter.com/bradygaster)

Este artigo fornece orientações para:

* Hospedagem de Hubs de SignalR usando um processo de trabalho em segundo plano hospedado com o ASP.NET Core.
* Enviando mensagens para clientes de dentro de um núcleo do .NET de conectados [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(como fazer o download)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>Conectar o SignalR durante a inicialização

Os Hubs de SignalR do ASP.NET Core no contexto de um processo de trabalho em segundo plano de hospedagem é idêntico à hospedagem de um Hub em um aplicativo web ASP.NET Core. No `Startup.ConfigureServices` chamar um método, `services.AddSignalR` adiciona os serviços necessários para a camada de injeção de dependência de núcleo do ASP.NET (DI) para dar suporte ao SignalR. Na `Startup.Configure`, o `UseSignalR` método é chamado para ligar os pontos de extremidade de Hub no pipeline de solicitação do ASP.NET Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

No exemplo anterior, o `ClockHub` classe implementa o `Hub<T>` classe para criar um Hub com rigidez de tipos. O `ClockHub` tiver sido configurado na `Startup` classe responder às solicitações no ponto de extremidade `/hubs/clock`.

Para obter mais informações sobre os Hubs com rigidez de tipos, consulte [usando os hubs de SignalR do ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Essa funcionalidade não está limitada para o [Hub\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) classe. Qualquer classe que herda de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), também funcionará.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

A interface usada pelo fortemente tipados `ClockHub` é o `IClock` interface.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Chamar um SignalR Hub de um serviço em segundo plano

Durante a inicialização, o `Worker` classe, uma `BackgroundService`, está conectada usando `AddHostedService`.

```csharp
services.AddHostedService<Worker>();
```

Uma vez que o SignalR também está conectado durante o `Startup` fase, em que cada Hub está anexado a um ponto de extremidade individual no pipeline de solicitação HTTP do ASP.NET Core, cada Hub é representado por um `IHubContext<T>` no servidor. Usando o ASP.NET Core injeção de dependência recursos, outras classes instanciadas por meio da camada de hospedagem, como `BackgroundService` classes, classes de controlador MVC ou modelos de página do Razor, podem obter referências para os Hubs do lado do servidor, aceitando as instâncias de `IHubContext<ClockHub, IClock>` durante a construção.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

Como o `ExecuteAsync` método é chamado de forma iterativa no serviço do plano de fundo, o servidor data e hora atuais são enviadas para os clientes conectados usando o `ClockHub`.

## <a name="react-to-signalr-events-with-background-services"></a>Reagir a eventos do SignalR com serviços em segundo plano

Como um aplicativo de página única usando o cliente JavaScript para um aplicativo de área de trabalho do .NET ou SignalR pode fazer usando o usando o <xref:signalr/dotnet-client>, um `BackgroundService` ou `IHostedService` implementação também pode ser usada para conectar-se aos Hubs de SignalR e responder a eventos.

O `ClockHubClient` classe implementa ambos o `IClock` interface e o `IHostedService` interface. Dessa forma, ele pode ser conectado durante `Startup` para executar continuamente e responder a eventos do Hub do servidor. 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante a inicialização, o `ClockHubClient` cria uma instância de um `HubConnection` e conecta os `IClock.ShowTime` método como o manipulador para o Hub `ShowTime` eventos.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

No `IHostedService.StartAsync` implementação, o `HubConnection` é iniciado de forma assíncrona.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante o `IHostedService.StopAsync` método, o `HubConnection` é descartado de forma assíncrona.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Recursos adicionais

* [Introdução](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Hubs com rigidez de tipos](xref:signalr/hubs#strongly-typed-hubs)
