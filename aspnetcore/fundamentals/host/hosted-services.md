---
title: Tarefas em segundo plano com serviços hospedados no ASP.NET Core
author: guardrex
description: Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 9b7224c07df027c9466db34dcc23505410893f1f
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171789"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Tarefas em segundo plano com serviços hospedados no ASP.NET Core

De [Luke Latham](https://github.com/guardrex) e [Jeow li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*. Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Este tópico fornece três exemplos de serviço hospedado:

* Tarefa em segundo plano que é executada com um temporizador.
* Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes). O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection).
* Tarefas em segundo plano na fila que são executadas sequencialmente.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Modelo de serviço de trabalho

O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada. Um aplicativo criado a partir do modelo de serviço de trabalho especifica o SDK do trabalhador em seu arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Para usar o modelo como base para um aplicativo de serviços hospedados:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Pacote

Um aplicativo baseado no modelo de serviço de trabalho usa o SDK do `Microsoft.NET.Sdk.Worker` e tem uma referência de pacote explícita para o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) . Por exemplo, consulte o arquivo de projeto do aplicativo de exemplo (*BackgroundTasksSample. csproj*).

Para aplicativos Web que usam o SDK do `Microsoft.NET.Sdk.Web`, o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) é referenciado implicitamente da estrutura compartilhada. Uma referência de pacote explícita no arquivo de projeto do aplicativo não é necessária.

## <a name="ihostedservice-interface"></a>Interface IHostedService

A interface <xref:Microsoft.Extensions.Hosting.IHostedService> define dois métodos para objetos que são gerenciados pelo host:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano. `StartAsync` é chamado *antes*de:

  * O pipeline de processamento de solicitação do aplicativo está configurado (`Startup.Configure`).
  * O servidor é iniciado e [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.

  O comportamento padrão pode ser alterado para que o `StartAsync` do serviço hospedado seja executado depois que o pipeline do aplicativo tiver sido configurado e `ApplicationStarted` for chamado. Para alterar o comportamento padrão, adicione o serviço hospedado (`VideosWatcher` no exemplo a seguir) depois de chamar `ConfigureWebHostDefaults`:

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

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; disparado quando o host está executando um desligamento normal. `StopAsync` contém a lógica para encerrar a tarefa em segundo plano. Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.

  O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal. Quando for solicitado um cancelamento no token:

  * Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.
  * Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.

  No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.

  Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado. Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.

  Para estender o tempo limite de desligamento padrão de cinco segundos, defina:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico. Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web. Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.

O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo. Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.

## <a name="backgroundservice-base-class"></a>Classe base BackgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService> é uma classe base para implementar um <xref:Microsoft.Extensions.Hosting.IHostedService>de longa execução.

[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) é chamado para executar o serviço em segundo plano. A implementação retorna um <xref:System.Threading.Tasks.Task> que representa o tempo de vida inteiro do serviço em segundo plano. Nenhum serviço adicional é iniciado até que [ExecuteAsync se torne assíncrono](https://github.com/dotnet/extensions/issues/2149), como chamando `await`. Evite executar tempo demorado, bloqueando o trabalho de inicialização no `ExecuteAsync`. O host bloqueia em [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) aguardando a conclusão de `ExecuteAsync`.

O token de cancelamento é disparado quando [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) é chamado. Sua implementação de `ExecuteAsync` deve ser concluída imediatamente quando o token de cancelamento é acionado para desligar o serviço normalmente. Caso contrário, o serviço será desligado sem nenhum normal no tempo limite do desligamento. Para obter mais informações, consulte a seção [interface IHostedService](#ihostedservice-interface) .

## <a name="timed-background-tasks"></a>Tarefas em segundo plano temporizadas

Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer). O temporizador dispara o método `DoWork` da tarefa. O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

O <xref:System.Threading.Timer> não aguarda que as execuções anteriores de `DoWork` sejam concluídas, portanto, a abordagem mostrada pode não ser adequada para cada cenário. [Interlocked. Increment](xref:System.Threading.Interlocked.Increment*) é usado para incrementar o contador de execução como uma operação atômica, o que garante que vários threads não sejam atualizados `executionCount` simultaneamente.

O serviço é registrado em `IHostBuilder.ConfigureServices` (*Program.cs*) com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumindo um serviço com escopo em uma tarefa em segundo plano

Para usar [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) em um [BackgroundService](#backgroundservice-base-class), crie um escopo. Por padrão, nenhum escopo é criado para um serviço hospedado.

O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano. No exemplo a seguir:

* O serviço é assíncrono. O método `DoWork` retorna um `Task`. Para fins de demonstração, um atraso de dez segundos é aguardado no método `DoWork`.
* Um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

O serviço hospedado cria um escopo para resolver o serviço de tarefa em segundo plano com escopo para chamar seu método de `DoWork`. `DoWork` retorna um `Task`, que é aguardado em `ExecuteAsync`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*). O serviço hospedado está registrado com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tarefas em segundo plano na fila

Uma fila de tarefas em segundo plano é baseada no <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> do .NET 4. x ([agendado provisoriamente para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

No exemplo a seguir `QueueHostedService`:

* O método `BackgroundProcessing` retorna um `Task`, que é aguardado em `ExecuteAsync`.
* As tarefas em segundo plano na fila são removidas da fila e executadas em `BackgroundProcessing`.
* Os itens de trabalho são aguardados antes de o serviço parar em `StopAsync`.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Um serviço de `MonitorLoop` trata das tarefas de enfileiramento para o serviço hospedado sempre que a chave de `w` é selecionada em um dispositivo de entrada:

* O `IBackgroundTaskQueue` é injetado no serviço `MonitorLoop`.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem` é chamado para enfileirar um item de trabalho.
* O item de trabalho simula uma tarefa em segundo plano de execução longa:
  * Três atrasos de 5 segundos são executados (`Task.Delay`).
  * Uma instrução `try-catch` intercepta <xref:System.OperationCanceledException> se a tarefa for cancelada.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*). O serviço hospedado está registrado com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MontiorLoop` é iniciado no `Program.Main`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*. Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. Este tópico fornece três exemplos de serviço hospedado:

* Tarefa em segundo plano que é executada com um temporizador.
* Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes). O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection)
* Tarefas em segundo plano na fila que são executadas sequencialmente.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="package"></a>Pacote

Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interface IHostedService

Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>. A interface define dois métodos para objetos que são gerenciados pelo host:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano. Ao usar o [host Web](xref:fundamentals/host/web-host), `StartAsync` é chamado depois que o servidor é iniciado e [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado. Ao usar o [host genérico](xref:fundamentals/host/generic-host), `StartAsync` é chamado antes de `ApplicationStarted` ser disparado.

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; disparado quando o host está executando um desligamento normal. `StopAsync` contém a lógica para encerrar a tarefa em segundo plano. Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.

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

O <xref:System.Threading.Timer> não aguarda que as execuções anteriores de `DoWork` sejam concluídas, portanto, a abordagem mostrada pode não ser adequada para cada cenário.

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

Uma fila de tarefas em segundo plano é baseada no <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> .NET Framework 4. x ([provisoriamente agendado para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um [BackgroundService](#backgroundservice-base-class), que é uma classe base para implementar um `IHostedService` de longa execução:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Os serviços são registrados em `Startup.ConfigureServices`. A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Na classe de modelo de página de índice:

* O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.
* Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`. O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo. Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado. `QueueBackgroundWorkItem` é chamado para enfileirar um item de trabalho:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Executar tarefas em segundo plano com trabalhos Web no serviço Azure App](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
