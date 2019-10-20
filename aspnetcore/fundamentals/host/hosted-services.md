---
title: Tarefas em segundo plano com serviços hospedados no ASP.NET Core
author: guardrex
description: Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c1fbb5ae8ffc4ee506f42df6a4cbbe845b2b903d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333652"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="163f8-103">Tarefas em segundo plano com serviços hospedados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="163f8-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="163f8-104">De [Luke Latham](https://github.com/guardrex) e [Jeow li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="163f8-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="163f8-105">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="163f8-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="163f8-106">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="163f8-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="163f8-107">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="163f8-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="163f8-108">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="163f8-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="163f8-109">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="163f8-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="163f8-110">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="163f8-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="163f8-111">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="163f8-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="163f8-112">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="163f8-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="163f8-113">Este aplicativo de exemplo é fornecido em duas versões:</span><span class="sxs-lookup"><span data-stu-id="163f8-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="163f8-114">Host da Web &ndash; O Host da Web é útil para hospedar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="163f8-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="163f8-115">O código de exemplo mostrado neste tópico é da versão do host da Web do exemplo.</span><span class="sxs-lookup"><span data-stu-id="163f8-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="163f8-116">Para obter mais informações, consulte o tópico [Host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="163f8-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="163f8-117">Host Genérico &ndash; O Host Genérico é novo no ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="163f8-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="163f8-118">Para obter mais informações, confira o tópico [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="163f8-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="163f8-119">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="163f8-119">Worker Service template</span></span>

<span data-ttu-id="163f8-120">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="163f8-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="163f8-121">Para usar o modelo como base para um aplicativo de serviços hospedados:</span><span class="sxs-lookup"><span data-stu-id="163f8-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a><span data-ttu-id="163f8-122">Pacote</span><span class="sxs-lookup"><span data-stu-id="163f8-122">Package</span></span>

<span data-ttu-id="163f8-123">Uma referência de pacote para o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) é adicionada implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="163f8-123">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="163f8-124">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="163f8-124">IHostedService interface</span></span>

<span data-ttu-id="163f8-125">A interface <xref:Microsoft.Extensions.Hosting.IHostedService> define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="163f8-125">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="163f8-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="163f8-127">`StartAsync` é chamado *antes*de:</span><span class="sxs-lookup"><span data-stu-id="163f8-127">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="163f8-128">O pipeline de processamento de solicitação do aplicativo está configurado (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="163f8-128">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="163f8-129">O servidor é iniciado e [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="163f8-129">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="163f8-130">O comportamento padrão pode ser alterado para que o `StartAsync` do serviço hospedado seja executado depois que o pipeline do aplicativo tiver sido configurado e `ApplicationStarted` for chamado.</span><span class="sxs-lookup"><span data-stu-id="163f8-130">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="163f8-131">Para alterar o comportamento padrão, adicione o serviço hospedado (`VideosWatcher` no exemplo a seguir) depois de chamar `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="163f8-131">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="163f8-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="163f8-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="163f8-133">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-133">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="163f8-134">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="163f8-134">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="163f8-135">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="163f8-135">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="163f8-136">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="163f8-136">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="163f8-137">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="163f8-137">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="163f8-138">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="163f8-138">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="163f8-139">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="163f8-139">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="163f8-140">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="163f8-140">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="163f8-141">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="163f8-141">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="163f8-142">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="163f8-142">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="163f8-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="163f8-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="163f8-144">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="163f8-144">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="163f8-145">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="163f8-145">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="163f8-146">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="163f8-146">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="163f8-147">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="163f8-147">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="163f8-148">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="163f8-148">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="163f8-149">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="163f8-149">BackgroundService</span></span>

<span data-ttu-id="163f8-150">`BackgroundService` é uma classe base para implementar um <xref:Microsoft.Extensions.Hosting.IHostedService> de longa execução.</span><span class="sxs-lookup"><span data-stu-id="163f8-150">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="163f8-151">`BackgroundService` fornece o método abstract `ExecuteAsync(CancellationToken stoppingToken)` para conter a lógica do serviço.</span><span class="sxs-lookup"><span data-stu-id="163f8-151">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="163f8-152">O `stoppingToken` é disparado quando [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) é chamado.</span><span class="sxs-lookup"><span data-stu-id="163f8-152">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="163f8-153">A implementação desse método retorna um `Task` que representa o tempo de vida inteiro do serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-153">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="163f8-154">Além disso, *opcionalmente* , substitua os métodos definidos em `IHostedService` para executar o código de inicialização e desligamento para seu serviço:</span><span class="sxs-lookup"><span data-stu-id="163f8-154">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="163f8-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` é chamado quando o host do aplicativo está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="163f8-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="163f8-156">A `cancellationToken` será sinalizada quando o host decidir forçar o encerramento do serviço.</span><span class="sxs-lookup"><span data-stu-id="163f8-156">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="163f8-157">Se esse método for substituído, você **deverá** chamar (e `await`) o método da classe base para garantir que o serviço seja desligado corretamente.</span><span class="sxs-lookup"><span data-stu-id="163f8-157">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="163f8-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` é chamado para iniciar o serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="163f8-159">O `cancellationToken` será sinalizado se o processo de inicialização for interrompido.</span><span class="sxs-lookup"><span data-stu-id="163f8-159">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="163f8-160">A implementação retorna um `Task` que representa o processo de inicialização para o serviço.</span><span class="sxs-lookup"><span data-stu-id="163f8-160">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="163f8-161">Nenhum serviço adicional será iniciado até que esse `Task` seja concluído.</span><span class="sxs-lookup"><span data-stu-id="163f8-161">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="163f8-162">Se esse método for substituído, você **deverá** chamar (e `await`) o método da classe base para garantir que o serviço seja iniciado corretamente.</span><span class="sxs-lookup"><span data-stu-id="163f8-162">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="163f8-163">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="163f8-163">Timed background tasks</span></span>

<span data-ttu-id="163f8-164">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="163f8-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="163f8-165">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="163f8-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="163f8-166">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="163f8-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="163f8-167">O serviço é registrado em `IHostBuilder.ConfigureServices` (*Program.cs*) com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="163f8-167">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="163f8-168">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="163f8-168">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="163f8-169">Para usar [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `BackgroundService`, crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="163f8-169">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="163f8-170">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="163f8-170">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="163f8-171">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-171">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="163f8-172">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="163f8-172">In the following example:</span></span>

* <span data-ttu-id="163f8-173">O serviço é assíncrono.</span><span class="sxs-lookup"><span data-stu-id="163f8-173">The service is asynchronous.</span></span> <span data-ttu-id="163f8-174">O método `DoWork` retorna um `Task`.</span><span class="sxs-lookup"><span data-stu-id="163f8-174">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="163f8-175">Para fins de demonstração, um atraso de dez segundos é aguardado no método `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="163f8-175">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="163f8-176">Um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço.</span><span class="sxs-lookup"><span data-stu-id="163f8-176">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="163f8-177">O serviço hospedado cria um escopo para resolver o serviço de tarefa em segundo plano com escopo para chamar seu método de `DoWork`.</span><span class="sxs-lookup"><span data-stu-id="163f8-177">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="163f8-178">`DoWork` retorna um `Task`, que é aguardado em `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="163f8-178">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="163f8-179">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="163f8-179">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="163f8-180">O serviço hospedado está registrado com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="163f8-180">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="163f8-181">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="163f8-181">Queued background tasks</span></span>

<span data-ttu-id="163f8-182">Uma fila de tarefas em segundo plano é baseada no <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> do .NET 4. x ([agendado provisoriamente para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="163f8-182">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="163f8-183">No exemplo a seguir `QueueHostedService`:</span><span class="sxs-lookup"><span data-stu-id="163f8-183">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="163f8-184">O método `BackgroundProcessing` retorna um `Task`, que é aguardado em `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="163f8-184">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="163f8-185">As tarefas em segundo plano na fila são removidas da fila e executadas em `BackgroundProcessing`.</span><span class="sxs-lookup"><span data-stu-id="163f8-185">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="163f8-186">Os itens de trabalho são aguardados antes de o serviço parar em `StopAsync`.</span><span class="sxs-lookup"><span data-stu-id="163f8-186">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="163f8-187">Um serviço de `MonitorLoop` trata das tarefas de enfileiramento para o serviço hospedado sempre que a chave de `w` é selecionada em um dispositivo de entrada:</span><span class="sxs-lookup"><span data-stu-id="163f8-187">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="163f8-188">O `IBackgroundTaskQueue` é injetado no serviço `MonitorLoop`.</span><span class="sxs-lookup"><span data-stu-id="163f8-188">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="163f8-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` é chamado para enfileirar um item de trabalho.</span><span class="sxs-lookup"><span data-stu-id="163f8-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="163f8-190">O item de trabalho simula uma tarefa em segundo plano de execução longa:</span><span class="sxs-lookup"><span data-stu-id="163f8-190">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="163f8-191">Três atrasos de 5 segundos são executados (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="163f8-191">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="163f8-192">Uma instrução `try-catch` intercepta <xref:System.OperationCanceledException> se a tarefa for cancelada.</span><span class="sxs-lookup"><span data-stu-id="163f8-192">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="163f8-193">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="163f8-193">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="163f8-194">O serviço hospedado está registrado com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="163f8-194">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="163f8-195">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="163f8-195">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="163f8-196">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="163f8-196">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="163f8-197">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="163f8-197">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="163f8-198">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="163f8-198">Background task that runs on a timer.</span></span>
* <span data-ttu-id="163f8-199">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="163f8-199">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="163f8-200">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="163f8-200">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="163f8-201">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="163f8-201">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="163f8-202">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="163f8-202">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="163f8-203">Este aplicativo de exemplo é fornecido em duas versões:</span><span class="sxs-lookup"><span data-stu-id="163f8-203">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="163f8-204">Host da Web &ndash; O Host da Web é útil para hospedar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="163f8-204">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="163f8-205">O código de exemplo mostrado neste tópico é da versão do host da Web do exemplo.</span><span class="sxs-lookup"><span data-stu-id="163f8-205">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="163f8-206">Para obter mais informações, consulte o tópico [Host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="163f8-206">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="163f8-207">Host Genérico &ndash; O Host Genérico é novo no ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="163f8-207">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="163f8-208">Para obter mais informações, confira o tópico [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="163f8-208">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="163f8-209">Pacote</span><span class="sxs-lookup"><span data-stu-id="163f8-209">Package</span></span>

<span data-ttu-id="163f8-210">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="163f8-210">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="163f8-211">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="163f8-211">IHostedService interface</span></span>

<span data-ttu-id="163f8-212">Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="163f8-212">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="163f8-213">A interface define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="163f8-213">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="163f8-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="163f8-215">Ao usar o [host Web](xref:fundamentals/host/web-host), `StartAsync` é chamado depois que o servidor é iniciado e [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="163f8-215">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="163f8-216">Ao usar o [host genérico](xref:fundamentals/host/generic-host), `StartAsync` é chamado antes de `ApplicationStarted` ser disparado.</span><span class="sxs-lookup"><span data-stu-id="163f8-216">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="163f8-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="163f8-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="163f8-218">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-218">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="163f8-219">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="163f8-219">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="163f8-220">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="163f8-220">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="163f8-221">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="163f8-221">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="163f8-222">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="163f8-222">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="163f8-223">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="163f8-223">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="163f8-224">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="163f8-224">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="163f8-225">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="163f8-225">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="163f8-226">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="163f8-226">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="163f8-227">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="163f8-227">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="163f8-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="163f8-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="163f8-229">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="163f8-229">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="163f8-230">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="163f8-230">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="163f8-231">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="163f8-231">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="163f8-232">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="163f8-232">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="163f8-233">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="163f8-233">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="163f8-234">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="163f8-234">Timed background tasks</span></span>

<span data-ttu-id="163f8-235">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="163f8-235">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="163f8-236">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="163f8-236">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="163f8-237">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="163f8-237">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="163f8-238">O serviço está registrado em `Startup.ConfigureServices` com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="163f8-238">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="163f8-239">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="163f8-239">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="163f8-240">Para usar os [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `IHostedService`, crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="163f8-240">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="163f8-241">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="163f8-241">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="163f8-242">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="163f8-242">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="163f8-243">No exemplo a seguir, um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço:</span><span class="sxs-lookup"><span data-stu-id="163f8-243">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="163f8-244">O serviço hospedado cria um escopo para resolver o serviço da tarefa em segundo plano com escopo para chamar seu método `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="163f8-244">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="163f8-245">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="163f8-245">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="163f8-246">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="163f8-246">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="163f8-247">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="163f8-247">Queued background tasks</span></span>

<span data-ttu-id="163f8-248">Uma fila de tarefas em segundo plano é baseada no <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> .NET Framework 4. x ([provisoriamente agendado para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="163f8-248">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="163f8-249">No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um <xref:Microsoft.Extensions.Hosting.BackgroundService>, que é uma classe base para implementar um `IHostedService` de longa execução:</span><span class="sxs-lookup"><span data-stu-id="163f8-249">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="163f8-250">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="163f8-250">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="163f8-251">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="163f8-251">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="163f8-252">Na classe de modelo de página de índice:</span><span class="sxs-lookup"><span data-stu-id="163f8-252">In the Index page model class:</span></span>

* <span data-ttu-id="163f8-253">O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.</span><span class="sxs-lookup"><span data-stu-id="163f8-253">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="163f8-254">Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="163f8-254">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="163f8-255">O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo.</span><span class="sxs-lookup"><span data-stu-id="163f8-255">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="163f8-256">Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).</span><span class="sxs-lookup"><span data-stu-id="163f8-256">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="163f8-257">Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado.</span><span class="sxs-lookup"><span data-stu-id="163f8-257">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="163f8-258">`QueueBackgroundWorkItem` é chamado para enfileirar um item de trabalho:</span><span class="sxs-lookup"><span data-stu-id="163f8-258">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="163f8-259">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="163f8-259">Additional resources</span></span>

* [<span data-ttu-id="163f8-260">Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="163f8-260">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
