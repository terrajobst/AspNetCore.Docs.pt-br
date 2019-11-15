---
title: Tarefas em segundo plano com serviços hospedados no ASP.NET Core
author: guardrex
description: Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 0fdf503e4a5f6f73d5488261707180cfb5967492
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115946"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="d8ce9-103">Tarefas em segundo plano com serviços hospedados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8ce9-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="d8ce9-104">De [Luke Latham](https://github.com/guardrex) e [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="d8ce9-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8ce9-105">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="d8ce9-106">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d8ce9-107">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="d8ce9-108">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="d8ce9-109">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d8ce9-110">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="d8ce9-111">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="d8ce9-112">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8ce9-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="d8ce9-113">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="d8ce9-113">Worker Service template</span></span>

<span data-ttu-id="d8ce9-114">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="d8ce9-115">Um aplicativo criado a partir do modelo de serviço de trabalho especifica o SDK do trabalhador em seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="d8ce9-116">Para usar o modelo como base para um aplicativo de serviços hospedados:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="d8ce9-117">Pacote</span><span class="sxs-lookup"><span data-stu-id="d8ce9-117">Package</span></span>

<span data-ttu-id="d8ce9-118">Um aplicativo baseado no modelo de serviço de trabalho usa o SDK do `Microsoft.NET.Sdk.Worker` e tem uma referência de pacote explícita para o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .</span><span class="sxs-lookup"><span data-stu-id="d8ce9-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="d8ce9-119">Por exemplo, consulte o arquivo de projeto do aplicativo de exemplo (*BackgroundTasksSample. csproj*).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="d8ce9-120">Para aplicativos Web que usam o SDK do `Microsoft.NET.Sdk.Web`, o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) é referenciado implicitamente da estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="d8ce9-121">Uma referência de pacote explícita no arquivo de projeto do aplicativo não é necessária.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="d8ce9-122">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="d8ce9-122">IHostedService interface</span></span>

<span data-ttu-id="d8ce9-123">A interface <xref:Microsoft.Extensions.Hosting.IHostedService> define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="d8ce9-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="d8ce9-125">`StartAsync` é chamado *antes*de:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="d8ce9-126">O pipeline de processamento de solicitação do aplicativo está configurado (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="d8ce9-127">O servidor é iniciado e [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="d8ce9-128">O comportamento padrão pode ser alterado para que o `StartAsync` do serviço hospedado seja executado depois que o pipeline do aplicativo tiver sido configurado e `ApplicationStarted` for chamado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="d8ce9-129">Para alterar o comportamento padrão, adicione o serviço hospedado (`VideosWatcher` no exemplo a seguir) depois de chamar `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="d8ce9-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="d8ce9-131">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="d8ce9-132">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="d8ce9-133">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="d8ce9-134">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="d8ce9-135">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="d8ce9-136">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="d8ce9-137">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="d8ce9-138">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="d8ce9-139">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="d8ce9-140">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="d8ce9-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="d8ce9-142">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="d8ce9-143">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="d8ce9-144">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="d8ce9-145">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="d8ce9-146">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="d8ce9-147">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="d8ce9-147">BackgroundService</span></span>

<span data-ttu-id="d8ce9-148">`BackgroundService` é uma classe base para implementar um <xref:Microsoft.Extensions.Hosting.IHostedService> de longa execução.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-148">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="d8ce9-149">`BackgroundService` fornece o método abstract `ExecuteAsync(CancellationToken stoppingToken)` para conter a lógica do serviço.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-149">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="d8ce9-150">O `stoppingToken` é disparado quando [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) é chamado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-150">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="d8ce9-151">A implementação desse método retorna um `Task` que representa o tempo de vida inteiro do serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-151">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="d8ce9-152">Além disso, *opcionalmente* , substitua os métodos definidos em `IHostedService` para executar o código de inicialização e desligamento para seu serviço:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-152">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="d8ce9-153">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` é chamado quando o host do aplicativo está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-153">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="d8ce9-154">A `cancellationToken` será sinalizada quando o host decidir forçar o encerramento do serviço.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-154">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="d8ce9-155">Se esse método for substituído, você **deverá** chamar (e `await`) o método da classe base para garantir que o serviço seja desligado corretamente.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-155">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="d8ce9-156">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` é chamado para iniciar o serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-156">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="d8ce9-157">O `cancellationToken` será sinalizado se o processo de inicialização for interrompido.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-157">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="d8ce9-158">A implementação retorna um `Task` que representa o processo de inicialização para o serviço.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-158">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="d8ce9-159">Nenhum serviço adicional será iniciado até que esse `Task` seja concluído.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-159">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="d8ce9-160">Se esse método for substituído, você **deverá** chamar (e `await`) o método da classe base para garantir que o serviço seja iniciado corretamente.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-160">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="d8ce9-161">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="d8ce9-161">Timed background tasks</span></span>

<span data-ttu-id="d8ce9-162">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-162">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="d8ce9-163">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-163">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="d8ce9-164">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-164">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="d8ce9-165">O serviço é registrado em `IHostBuilder.ConfigureServices` (*Program.cs*) com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-165">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="d8ce9-166">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="d8ce9-166">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="d8ce9-167">Para usar [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `BackgroundService`, crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-167">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="d8ce9-168">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-168">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="d8ce9-169">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-169">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="d8ce9-170">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-170">In the following example:</span></span>

* <span data-ttu-id="d8ce9-171">O serviço é assíncrono.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-171">The service is asynchronous.</span></span> <span data-ttu-id="d8ce9-172">O método `DoWork` retorna um `Task`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-172">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="d8ce9-173">Para fins de demonstração, um atraso de dez segundos é aguardado no método `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-173">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="d8ce9-174">Um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-174">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="d8ce9-175">O serviço hospedado cria um escopo para resolver o serviço de tarefa em segundo plano com escopo para chamar seu método de `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-175">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="d8ce9-176">`DoWork` retorna um `Task`, que é aguardado em `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-176">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="d8ce9-177">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-177">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="d8ce9-178">O serviço hospedado está registrado com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-178">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="d8ce9-179">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="d8ce9-179">Queued background tasks</span></span>

<span data-ttu-id="d8ce9-180">Uma fila de tarefas em segundo plano é baseada no <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> do .NET 4. x ([agendado provisoriamente para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="d8ce9-180">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="d8ce9-181">No exemplo a seguir `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-181">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="d8ce9-182">O método `BackgroundProcessing` retorna um `Task`, que é aguardado em `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-182">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="d8ce9-183">As tarefas em segundo plano na fila são removidas da fila e executadas em `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-183">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="d8ce9-184">Os itens de trabalho são aguardados antes de o serviço parar em `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-184">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="d8ce9-185">Um serviço de `MonitorLoop` trata das tarefas de enfileiramento para o serviço hospedado sempre que a chave de `w` é selecionada em um dispositivo de entrada:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-185">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="d8ce9-186">O `IBackgroundTaskQueue` é injetado no serviço `MonitorLoop`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-186">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="d8ce9-187">`IBackgroundTaskQueue.QueueBackgroundWorkItem` é chamado para enfileirar um item de trabalho.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-187">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="d8ce9-188">O item de trabalho simula uma tarefa em segundo plano de execução longa:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-188">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="d8ce9-189">Três atrasos de 5 segundos são executados (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-189">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="d8ce9-190">Uma instrução `try-catch` intercepta <xref:System.OperationCanceledException> se a tarefa for cancelada.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-190">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="d8ce9-191">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-191">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="d8ce9-192">O serviço hospedado está registrado com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-192">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8ce9-193">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="d8ce9-194">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d8ce9-195">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="d8ce9-196">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="d8ce9-197">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d8ce9-198">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d8ce9-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="d8ce9-199">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="d8ce9-200">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d8ce9-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="d8ce9-201">Pacote</span><span class="sxs-lookup"><span data-stu-id="d8ce9-201">Package</span></span>

<span data-ttu-id="d8ce9-202">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="d8ce9-203">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="d8ce9-203">IHostedService interface</span></span>

<span data-ttu-id="d8ce9-204">Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d8ce9-205">A interface define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="d8ce9-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="d8ce9-207">Ao usar o [host Web](xref:fundamentals/host/web-host), `StartAsync` é chamado depois que o servidor é iniciado e [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="d8ce9-208">Ao usar o [host genérico](xref:fundamentals/host/generic-host), `StartAsync` é chamado antes de `ApplicationStarted` ser disparado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="d8ce9-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="d8ce9-210">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="d8ce9-211">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="d8ce9-212">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="d8ce9-213">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="d8ce9-214">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="d8ce9-215">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="d8ce9-216">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="d8ce9-217">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="d8ce9-218">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="d8ce9-219">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="d8ce9-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="d8ce9-221">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="d8ce9-222">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="d8ce9-223">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="d8ce9-224">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="d8ce9-225">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="d8ce9-226">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="d8ce9-226">Timed background tasks</span></span>

<span data-ttu-id="d8ce9-227">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="d8ce9-228">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="d8ce9-229">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="d8ce9-230">O serviço está registrado em `Startup.ConfigureServices` com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-230">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="d8ce9-231">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="d8ce9-231">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="d8ce9-232">Para usar os [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `IHostedService`, crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-232">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="d8ce9-233">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-233">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="d8ce9-234">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-234">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="d8ce9-235">No exemplo a seguir, um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-235">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="d8ce9-236">O serviço hospedado cria um escopo para resolver o serviço da tarefa em segundo plano com escopo para chamar seu método `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-236">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="d8ce9-237">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-237">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d8ce9-238">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-238">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="d8ce9-239">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="d8ce9-239">Queued background tasks</span></span>

<span data-ttu-id="d8ce9-240">Uma fila de tarefas em segundo plano é baseada no <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> .NET Framework 4. x ([provisoriamente agendado para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="d8ce9-240">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="d8ce9-241">No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um <xref:Microsoft.Extensions.Hosting.BackgroundService>, que é uma classe base para implementar um `IHostedService` de longa execução:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-241">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="d8ce9-242">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-242">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d8ce9-243">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-243">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="d8ce9-244">Na classe de modelo de página de índice:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-244">In the Index page model class:</span></span>

* <span data-ttu-id="d8ce9-245">O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-245">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="d8ce9-246">Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-246">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="d8ce9-247">O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-247">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="d8ce9-248">Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).</span><span class="sxs-lookup"><span data-stu-id="d8ce9-248">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d8ce9-249">Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado.</span><span class="sxs-lookup"><span data-stu-id="d8ce9-249">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="d8ce9-250">`QueueBackgroundWorkItem` é chamado para enfileirar um item de trabalho:</span><span class="sxs-lookup"><span data-stu-id="d8ce9-250">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d8ce9-251">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d8ce9-251">Additional resources</span></span>

* [<span data-ttu-id="d8ce9-252">Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="d8ce9-252">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
