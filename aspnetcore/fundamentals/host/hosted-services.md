---
title: Tarefas em segundo plano com serviços hospedados no ASP.NET Core
author: guardrex
description: Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: da3c2679005714a3d82de94cf3bc3c809aa3500d
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239727"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Tarefas em segundo plano com serviços hospedados no ASP.NET Core

By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*. Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Este tópico fornece três exemplos de serviço hospedado:

* Tarefa em segundo plano que é executada com um temporizador.
* Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes). The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).
* Tarefas em segundo plano na fila que são executadas sequencialmente.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Modelo de serviço de trabalho

O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada. An app created from the Worker Service template specifies the Worker SDK in its project file:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Para usar o modelo como base para um aplicativo de serviços hospedados:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Pacote

An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package. For example, see the sample app's project file (*BackgroundTasksSample.csproj*).

For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework. An explicit package reference in the app's project file isn't required.

## <a name="ihostedservice-interface"></a>Interface IHostedService

The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano. `StartAsync` is called *before*:

  * The app's request processing pipeline is configured (`Startup.Configure`).
  * The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.

  The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called. To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal. `StopAsync` contém a lógica para encerrar a tarefa em segundo plano. Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.

  O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal. Quando for solicitado um cancelamento no token:

  * Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.
  * Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.

  No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.

  Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado. Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.

  Para estender o tempo limite de desligamento padrão de cinco segundos, defina:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico. Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web. Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.

O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo. Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.

## <a name="backgroundservice-base-class"></a>BackgroundService base class

<xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.

[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service. The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service. No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/aspnet/Extensions/issues/2149), such as by calling `await`. Avoid performing long, blocking initialization work in `ExecuteAsync`. The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.

The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called. Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service. Otherwise, the service ungracefully shuts down at the shutdown timeout. For more information, see the [IHostedService interface](#ihostedservice-interface) section.

## <a name="timed-background-tasks"></a>Tarefas em segundo plano temporizadas

Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer). O temporizador dispara o método `DoWork` da tarefa. O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumindo um serviço com escopo em uma tarefa em segundo plano

To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope. Por padrão, nenhum escopo é criado para um serviço hospedado.

O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano. No exemplo a seguir:

* The service is asynchronous. O método `DoWork` retorna um `Task`. For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.
* An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method. `DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*). The hosted service is registered with the `AddHostedService` extension method:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tarefas em segundo plano na fila

A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

In the following `QueueHostedService` example:

* The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.
* Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.
* Work items are awaited before the service stops in `StopAsync`.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:

* The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.
* The work item simulates a long-running background task:
  * Three 5-second delays are executed (`Task.Delay`).
  * A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*). The hosted service is registered with the `AddHostedService` extension method:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*. Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Este tópico fornece três exemplos de serviço hospedado:

* Tarefa em segundo plano que é executada com um temporizador.
* Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes). The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)
* Tarefas em segundo plano na fila que são executadas sequencialmente.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pacote

Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interface IHostedService

Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. A interface define dois métodos para objetos que são gerenciados pelo host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano. Ao usar o [host Web](xref:fundamentals/host/web-host), `StartAsync` é chamado depois que o servidor é iniciado e [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado. Ao usar o [host genérico](xref:fundamentals/host/generic-host), `StartAsync` é chamado antes de `ApplicationStarted` ser disparado.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal. `StopAsync` contém a lógica para encerrar a tarefa em segundo plano. Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.

  O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal. Quando for solicitado um cancelamento no token:

  * Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.
  * Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.

  No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.

  Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado. Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.

  Para estender o tempo limite de desligamento padrão de cinco segundos, defina:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico. Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web. Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.

O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo. Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.

## <a name="timed-background-tasks"></a>Tarefas em segundo plano temporizadas

Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer). O temporizador dispara o método `DoWork` da tarefa. O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

O serviço está registrado em `Startup.ConfigureServices` com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumindo um serviço com escopo em uma tarefa em segundo plano

Para usar os [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `IHostedService`, crie um escopo. Por padrão, nenhum escopo é criado para um serviço hospedado.

O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano. No exemplo a seguir, um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

O serviço hospedado cria um escopo para resolver o serviço da tarefa em segundo plano com escopo para chamar seu método `DoWork`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Os serviços são registrados em `Startup.ConfigureServices`. A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tarefas em segundo plano na fila

A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um [BackgroundService](#backgroundservice-base-class), que é uma classe base para implementar um `IHostedService` de longa execução:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Os serviços são registrados em `Startup.ConfigureServices`. A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Na classe de modelo de página de índice:

* O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.
* Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`. O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo. Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado. `QueueBackgroundWorkItem` is called to enqueue a work item:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
