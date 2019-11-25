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
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="36836-103">Tarefas em segundo plano com serviços hospedados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36836-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="36836-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="36836-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36836-105">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="36836-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="36836-106">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="36836-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="36836-107">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="36836-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="36836-108">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="36836-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="36836-109">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="36836-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="36836-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36836-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="36836-111">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="36836-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="36836-112">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36836-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="36836-113">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="36836-113">Worker Service template</span></span>

<span data-ttu-id="36836-114">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="36836-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="36836-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span><span class="sxs-lookup"><span data-stu-id="36836-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="36836-116">Para usar o modelo como base para um aplicativo de serviços hospedados:</span><span class="sxs-lookup"><span data-stu-id="36836-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="36836-117">Pacote</span><span class="sxs-lookup"><span data-stu-id="36836-117">Package</span></span>

<span data-ttu-id="36836-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span><span class="sxs-lookup"><span data-stu-id="36836-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="36836-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span><span class="sxs-lookup"><span data-stu-id="36836-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="36836-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span><span class="sxs-lookup"><span data-stu-id="36836-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="36836-121">An explicit package reference in the app's project file isn't required.</span><span class="sxs-lookup"><span data-stu-id="36836-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="36836-122">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="36836-122">IHostedService interface</span></span>

<span data-ttu-id="36836-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span><span class="sxs-lookup"><span data-stu-id="36836-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="36836-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="36836-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="36836-125">`StartAsync` is called *before*:</span><span class="sxs-lookup"><span data-stu-id="36836-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="36836-126">The app's request processing pipeline is configured (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="36836-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="36836-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span><span class="sxs-lookup"><span data-stu-id="36836-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="36836-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span><span class="sxs-lookup"><span data-stu-id="36836-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="36836-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="36836-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="36836-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36836-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="36836-131">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="36836-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="36836-132">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="36836-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="36836-133">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="36836-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="36836-134">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="36836-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="36836-135">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="36836-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="36836-136">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="36836-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="36836-137">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="36836-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="36836-138">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="36836-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="36836-139">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="36836-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="36836-140">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="36836-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="36836-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="36836-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="36836-142">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="36836-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="36836-143">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="36836-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="36836-144">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="36836-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="36836-145">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36836-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="36836-146">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="36836-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="36836-147">BackgroundService base class</span><span class="sxs-lookup"><span data-stu-id="36836-147">BackgroundService base class</span></span>

<span data-ttu-id="36836-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="36836-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="36836-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span><span class="sxs-lookup"><span data-stu-id="36836-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="36836-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span><span class="sxs-lookup"><span data-stu-id="36836-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="36836-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/aspnet/Extensions/issues/2149), such as by calling `await`.</span><span class="sxs-lookup"><span data-stu-id="36836-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/aspnet/Extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="36836-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="36836-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="36836-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span><span class="sxs-lookup"><span data-stu-id="36836-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="36836-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span><span class="sxs-lookup"><span data-stu-id="36836-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="36836-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span><span class="sxs-lookup"><span data-stu-id="36836-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="36836-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span><span class="sxs-lookup"><span data-stu-id="36836-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="36836-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span><span class="sxs-lookup"><span data-stu-id="36836-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="36836-158">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="36836-158">Timed background tasks</span></span>

<span data-ttu-id="36836-159">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="36836-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="36836-160">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="36836-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="36836-161">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="36836-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="36836-162">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="36836-162">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="36836-163">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="36836-163">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="36836-164">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span><span class="sxs-lookup"><span data-stu-id="36836-164">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="36836-165">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="36836-165">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="36836-166">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="36836-166">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="36836-167">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="36836-167">In the following example:</span></span>

* <span data-ttu-id="36836-168">The service is asynchronous.</span><span class="sxs-lookup"><span data-stu-id="36836-168">The service is asynchronous.</span></span> <span data-ttu-id="36836-169">O método `DoWork` retorna um `Task`.</span><span class="sxs-lookup"><span data-stu-id="36836-169">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="36836-170">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span><span class="sxs-lookup"><span data-stu-id="36836-170">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="36836-171">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span><span class="sxs-lookup"><span data-stu-id="36836-171">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="36836-172">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span><span class="sxs-lookup"><span data-stu-id="36836-172">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="36836-173">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="36836-173">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="36836-174">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="36836-174">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="36836-175">The hosted service is registered with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="36836-175">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="36836-176">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="36836-176">Queued background tasks</span></span>

<span data-ttu-id="36836-177">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="36836-177">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="36836-178">In the following `QueueHostedService` example:</span><span class="sxs-lookup"><span data-stu-id="36836-178">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="36836-179">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="36836-179">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="36836-180">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="36836-180">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="36836-181">Work items are awaited before the service stops in `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="36836-181">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="36836-182">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span><span class="sxs-lookup"><span data-stu-id="36836-182">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="36836-183">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span><span class="sxs-lookup"><span data-stu-id="36836-183">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="36836-184">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span><span class="sxs-lookup"><span data-stu-id="36836-184">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="36836-185">The work item simulates a long-running background task:</span><span class="sxs-lookup"><span data-stu-id="36836-185">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="36836-186">Three 5-second delays are executed (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="36836-186">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="36836-187">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span><span class="sxs-lookup"><span data-stu-id="36836-187">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="36836-188">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="36836-188">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="36836-189">The hosted service is registered with the `AddHostedService` extension method:</span><span class="sxs-lookup"><span data-stu-id="36836-189">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36836-190">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="36836-190">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="36836-191">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="36836-191">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="36836-192">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="36836-192">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="36836-193">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="36836-193">Background task that runs on a timer.</span></span>
* <span data-ttu-id="36836-194">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="36836-194">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="36836-195">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="36836-195">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="36836-196">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="36836-196">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="36836-197">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36836-197">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="36836-198">Pacote</span><span class="sxs-lookup"><span data-stu-id="36836-198">Package</span></span>

<span data-ttu-id="36836-199">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="36836-199">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="36836-200">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="36836-200">IHostedService interface</span></span>

<span data-ttu-id="36836-201">Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="36836-201">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="36836-202">A interface define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="36836-202">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="36836-203">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="36836-203">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="36836-204">Ao usar o [host Web](xref:fundamentals/host/web-host), `StartAsync` é chamado depois que o servidor é iniciado e [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="36836-204">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="36836-205">Ao usar o [host genérico](xref:fundamentals/host/generic-host), `StartAsync` é chamado antes de `ApplicationStarted` ser disparado.</span><span class="sxs-lookup"><span data-stu-id="36836-205">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="36836-206">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36836-206">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="36836-207">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="36836-207">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="36836-208">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="36836-208">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="36836-209">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="36836-209">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="36836-210">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="36836-210">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="36836-211">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="36836-211">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="36836-212">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="36836-212">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="36836-213">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="36836-213">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="36836-214">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="36836-214">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="36836-215">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="36836-215">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="36836-216">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="36836-216">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="36836-217"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="36836-217"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="36836-218">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="36836-218">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="36836-219">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="36836-219">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="36836-220">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="36836-220">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="36836-221">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36836-221">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="36836-222">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="36836-222">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="36836-223">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="36836-223">Timed background tasks</span></span>

<span data-ttu-id="36836-224">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="36836-224">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="36836-225">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="36836-225">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="36836-226">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="36836-226">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="36836-227">O serviço está registrado em `Startup.ConfigureServices` com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="36836-227">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="36836-228">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="36836-228">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="36836-229">Para usar os [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `IHostedService`, crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="36836-229">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="36836-230">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="36836-230">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="36836-231">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="36836-231">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="36836-232">No exemplo a seguir, um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço:</span><span class="sxs-lookup"><span data-stu-id="36836-232">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="36836-233">O serviço hospedado cria um escopo para resolver o serviço da tarefa em segundo plano com escopo para chamar seu método `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="36836-233">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="36836-234">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="36836-234">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36836-235">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="36836-235">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="36836-236">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="36836-236">Queued background tasks</span></span>

<span data-ttu-id="36836-237">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="36836-237">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="36836-238">No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um [BackgroundService](#backgroundservice-base-class), que é uma classe base para implementar um `IHostedService` de longa execução:</span><span class="sxs-lookup"><span data-stu-id="36836-238">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="36836-239">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="36836-239">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36836-240">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="36836-240">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="36836-241">Na classe de modelo de página de índice:</span><span class="sxs-lookup"><span data-stu-id="36836-241">In the Index page model class:</span></span>

* <span data-ttu-id="36836-242">O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.</span><span class="sxs-lookup"><span data-stu-id="36836-242">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="36836-243">Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="36836-243">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="36836-244">O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo.</span><span class="sxs-lookup"><span data-stu-id="36836-244">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="36836-245">Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).</span><span class="sxs-lookup"><span data-stu-id="36836-245">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="36836-246">Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado.</span><span class="sxs-lookup"><span data-stu-id="36836-246">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="36836-247">`QueueBackgroundWorkItem` is called to enqueue a work item:</span><span class="sxs-lookup"><span data-stu-id="36836-247">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="36836-248">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="36836-248">Additional resources</span></span>

* [<span data-ttu-id="36836-249">Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="36836-249">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
