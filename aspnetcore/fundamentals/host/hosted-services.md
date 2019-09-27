---
title: Tarefas em segundo plano com serviços hospedados no ASP.NET Core
author: guardrex
description: Aprenda a implementar tarefas em segundo plano com serviços hospedados no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 5a29952c4e50edb953fa03c6ea1a1ae27b728bb0
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317717"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="87385-103">Tarefas em segundo plano com serviços hospedados no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="87385-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="87385-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="87385-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="87385-105">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="87385-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="87385-106">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="87385-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="87385-107">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="87385-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="87385-108">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="87385-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="87385-109">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="87385-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="87385-110">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="87385-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="87385-111">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="87385-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="87385-112">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="87385-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="87385-113">Este aplicativo de exemplo é fornecido em duas versões:</span><span class="sxs-lookup"><span data-stu-id="87385-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="87385-114">Host da Web &ndash; O Host da Web é útil para hospedar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="87385-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="87385-115">O código de exemplo mostrado neste tópico é da versão do host da Web do exemplo.</span><span class="sxs-lookup"><span data-stu-id="87385-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="87385-116">Para obter mais informações, consulte o tópico [Host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="87385-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="87385-117">Host Genérico &ndash; O Host Genérico é novo no ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="87385-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="87385-118">Para obter mais informações, confira o tópico [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="87385-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="87385-119">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="87385-119">Worker Service template</span></span>

<span data-ttu-id="87385-120">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="87385-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="87385-121">Para usar o modelo como base para um aplicativo de serviços hospedados:</span><span class="sxs-lookup"><span data-stu-id="87385-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="87385-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87385-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="87385-123">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="87385-123">Create a new project.</span></span>
1. <span data-ttu-id="87385-124">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="87385-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="87385-125">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="87385-125">Select **Next**.</span></span>
1. <span data-ttu-id="87385-126">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="87385-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="87385-127">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="87385-127">Select **Create**.</span></span>
1. <span data-ttu-id="87385-128">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 3.0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="87385-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="87385-129">Selecione o modelo **Serviço de Trabalho**.</span><span class="sxs-lookup"><span data-stu-id="87385-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="87385-130">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="87385-130">Select **Create**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="87385-131">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="87385-131">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="87385-132">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="87385-132">Create a new project.</span></span>
1. <span data-ttu-id="87385-133">Selecione **aplicativo** em **.NET Core** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="87385-133">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="87385-134">Selecione **trabalhador** em **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="87385-134">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="87385-135">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="87385-135">Select **Next**.</span></span>
1. <span data-ttu-id="87385-136">Selecione **.NET Core 3,0** para a **estrutura de destino**.</span><span class="sxs-lookup"><span data-stu-id="87385-136">Select **.NET Core 3.0** for the **Target Framework**.</span></span> <span data-ttu-id="87385-137">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="87385-137">Select **Next**.</span></span>
1. <span data-ttu-id="87385-138">Forneça um nome no campo **nome do projeto** .</span><span class="sxs-lookup"><span data-stu-id="87385-138">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="87385-139">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="87385-139">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="87385-140">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="87385-140">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="87385-141">Use o modelo de Serviço de Trabalho (`worker`) com o comando [dotnet novo](/dotnet/core/tools/dotnet-new) em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="87385-141">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="87385-142">No exemplo a seguir, um aplicativo de Serviço de Trabalho é criado com o nome `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="87385-142">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="87385-143">Uma pasta para o aplicativo `ContosoWorker` é criada automaticamente quando o comando é executado.</span><span class="sxs-lookup"><span data-stu-id="87385-143">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---

## <a name="package"></a><span data-ttu-id="87385-144">Pacote</span><span class="sxs-lookup"><span data-stu-id="87385-144">Package</span></span>

<span data-ttu-id="87385-145">Uma referência de pacote para o pacote [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) é adicionada implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="87385-145">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="87385-146">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="87385-146">IHostedService interface</span></span>

<span data-ttu-id="87385-147">A <xref:Microsoft.Extensions.Hosting.IHostedService> interface define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="87385-147">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="87385-148">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-148">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="87385-149">`StartAsync`é chamado *antes*de:</span><span class="sxs-lookup"><span data-stu-id="87385-149">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="87385-150">O pipeline de processamento de solicitação do aplicativo está`Startup.Configure`configurado ().</span><span class="sxs-lookup"><span data-stu-id="87385-150">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="87385-151">O servidor é iniciado e [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="87385-151">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="87385-152">O comportamento padrão pode ser alterado para que o serviço `StartAsync` hospedado seja executado depois que o pipeline do aplicativo tiver sido configurado e `ApplicationStarted` chamado.</span><span class="sxs-lookup"><span data-stu-id="87385-152">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="87385-153">Para alterar o comportamento padrão, adicione o serviço hospedado (`VideosWatcher` no exemplo a seguir) após chamar `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="87385-153">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="87385-154">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="87385-154">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="87385-155">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-155">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="87385-156">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="87385-156">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="87385-157">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="87385-157">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="87385-158">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="87385-158">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="87385-159">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="87385-159">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="87385-160">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="87385-160">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="87385-161">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="87385-161">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="87385-162">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="87385-162">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="87385-163">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="87385-163">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="87385-164">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="87385-164">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="87385-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="87385-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="87385-166">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="87385-166">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="87385-167">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="87385-167">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="87385-168">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="87385-168">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="87385-169">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="87385-169">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="87385-170">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="87385-170">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="87385-171">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="87385-171">BackgroundService</span></span>

<span data-ttu-id="87385-172">`BackgroundService`é uma classe base para implementar uma longa execução <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="87385-172">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="87385-173">`BackgroundService`fornece o `ExecuteAsync(CancellationToken stoppingToken)` método abstract para conter a lógica do serviço.</span><span class="sxs-lookup"><span data-stu-id="87385-173">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="87385-174">O `stoppingToken` é disparado quando [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) é chamado.</span><span class="sxs-lookup"><span data-stu-id="87385-174">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="87385-175">A implementação desse método retorna um `Task` que representa o tempo de vida inteiro do serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-175">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="87385-176">Além disso, *opcionalmente* , substitua os métodos definidos `IHostedService` em para executar o código de inicialização e de desligamento para seu serviço:</span><span class="sxs-lookup"><span data-stu-id="87385-176">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="87385-177">`StopAsync(CancellationToken cancellationToken)`&ndash; échamadoquandoohostdeaplicativoestáexecutandoum`StopAsync` desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="87385-177">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="87385-178">O `cancellationToken` será sinalizado quando o host decidir forçar o encerramento do serviço.</span><span class="sxs-lookup"><span data-stu-id="87385-178">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="87385-179">Se esse método for substituído, você **deverá** chamar (e `await`) o método de classe base para garantir que o serviço seja desligado corretamente.</span><span class="sxs-lookup"><span data-stu-id="87385-179">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="87385-180">`StartAsync(CancellationToken cancellationToken)`&ndash; échamadoparainiciaro`StartAsync` serviço em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-180">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="87385-181">O `cancellationToken` será sinalizado se o processo de inicialização for interrompido.</span><span class="sxs-lookup"><span data-stu-id="87385-181">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="87385-182">A implementação retorna um `Task` que representa o processo de inicialização para o serviço.</span><span class="sxs-lookup"><span data-stu-id="87385-182">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="87385-183">Nenhum serviço adicional é iniciado até que `Task` isso seja concluído.</span><span class="sxs-lookup"><span data-stu-id="87385-183">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="87385-184">Se esse método for substituído, você **deverá** chamar (e `await`) o método de classe base para garantir que o serviço seja iniciado corretamente.</span><span class="sxs-lookup"><span data-stu-id="87385-184">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="87385-185">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="87385-185">Timed background tasks</span></span>

<span data-ttu-id="87385-186">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="87385-186">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="87385-187">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="87385-187">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="87385-188">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="87385-188">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="87385-189">O serviço está registrado em `IHostBuilder.ConfigureServices` (*Program.cs*) com o `AddHostedService` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="87385-189">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="87385-190">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="87385-190">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="87385-191">Para usar [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de `BackgroundService`um, crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="87385-191">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="87385-192">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="87385-192">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="87385-193">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-193">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="87385-194">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="87385-194">In the following example:</span></span>

* <span data-ttu-id="87385-195">O serviço é assíncrono.</span><span class="sxs-lookup"><span data-stu-id="87385-195">The service is asynchronous.</span></span> <span data-ttu-id="87385-196">O método `DoWork` retorna um `Task`.</span><span class="sxs-lookup"><span data-stu-id="87385-196">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="87385-197">Para fins de demonstração, um atraso de dez segundos é aguardado no `DoWork` método.</span><span class="sxs-lookup"><span data-stu-id="87385-197">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="87385-198">Um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço.</span><span class="sxs-lookup"><span data-stu-id="87385-198">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="87385-199">O serviço hospedado cria um escopo para resolver o serviço de tarefa em segundo plano com escopo `DoWork` para chamar seu método.</span><span class="sxs-lookup"><span data-stu-id="87385-199">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="87385-200">`DoWork`Retorna um `Task`, que é esperado em `ExecuteAsync`:</span><span class="sxs-lookup"><span data-stu-id="87385-200">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="87385-201">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="87385-201">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="87385-202">O serviço hospedado está registrado com o `AddHostedService` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="87385-202">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="87385-203">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="87385-203">Queued background tasks</span></span>

<span data-ttu-id="87385-204">Uma fila de tarefas em segundo plano é baseada no .NET 4 <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> . x ([provisoriamente agendado para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="87385-204">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="87385-205">No exemplo a `QueueHostedService` seguir:</span><span class="sxs-lookup"><span data-stu-id="87385-205">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="87385-206">O `BackgroundProcessing` método retorna um `Task`, que é esperado em `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="87385-206">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="87385-207">As tarefas em segundo plano na fila são removidas da `BackgroundProcessing`fila e executadas no.</span><span class="sxs-lookup"><span data-stu-id="87385-207">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

<span data-ttu-id="87385-208">Um `MonitorLoop` serviço lida com tarefas de enfileiramento para o serviço `w` hospedado sempre que a chave é selecionada em um dispositivo de entrada:</span><span class="sxs-lookup"><span data-stu-id="87385-208">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="87385-209">O `IBackgroundTaskQueue` é injetado `MonitorLoop` no serviço.</span><span class="sxs-lookup"><span data-stu-id="87385-209">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="87385-210">`IBackgroundTaskQueue.QueueBackgroundWorkItem`é chamado para enfileirar um item de trabalho.</span><span class="sxs-lookup"><span data-stu-id="87385-210">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="87385-211">Os serviços são registrados em `IHostBuilder.ConfigureServices` (*Program.cs*).</span><span class="sxs-lookup"><span data-stu-id="87385-211">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="87385-212">O serviço hospedado está registrado com o `AddHostedService` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="87385-212">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87385-213">No ASP.NET Core, as tarefas em segundo plano podem ser implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="87385-213">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="87385-214">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="87385-214">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="87385-215">Este tópico fornece três exemplos de serviço hospedado:</span><span class="sxs-lookup"><span data-stu-id="87385-215">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="87385-216">Tarefa em segundo plano que é executada com um temporizador.</span><span class="sxs-lookup"><span data-stu-id="87385-216">Background task that runs on a timer.</span></span>
* <span data-ttu-id="87385-217">Serviço hospedado que ativa um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="87385-217">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="87385-218">O serviço com escopo pode usar [injeção de dependência (di)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="87385-218">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="87385-219">Tarefas em segundo plano na fila que são executadas sequencialmente.</span><span class="sxs-lookup"><span data-stu-id="87385-219">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="87385-220">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="87385-220">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="87385-221">Este aplicativo de exemplo é fornecido em duas versões:</span><span class="sxs-lookup"><span data-stu-id="87385-221">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="87385-222">Host da Web &ndash; O Host da Web é útil para hospedar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="87385-222">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="87385-223">O código de exemplo mostrado neste tópico é da versão do host da Web do exemplo.</span><span class="sxs-lookup"><span data-stu-id="87385-223">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="87385-224">Para obter mais informações, consulte o tópico [Host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="87385-224">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="87385-225">Host Genérico &ndash; O Host Genérico é novo no ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="87385-225">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="87385-226">Para obter mais informações, confira o tópico [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="87385-226">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="87385-227">Pacote</span><span class="sxs-lookup"><span data-stu-id="87385-227">Package</span></span>

<span data-ttu-id="87385-228">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).</span><span class="sxs-lookup"><span data-stu-id="87385-228">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="87385-229">Interface IHostedService</span><span class="sxs-lookup"><span data-stu-id="87385-229">IHostedService interface</span></span>

<span data-ttu-id="87385-230">Os serviços hospedados implementam a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="87385-230">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="87385-231">A interface define dois métodos para objetos que são gerenciados pelo host:</span><span class="sxs-lookup"><span data-stu-id="87385-231">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="87385-232">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contém a lógica para iniciar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-232">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="87385-233">Ao usar o [host Web](xref:fundamentals/host/web-host), `StartAsync` é chamado depois que o servidor é iniciado e [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) é disparado.</span><span class="sxs-lookup"><span data-stu-id="87385-233">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="87385-234">Ao usar o [host genérico](xref:fundamentals/host/generic-host), `StartAsync` é chamado antes de `ApplicationStarted` ser disparado.</span><span class="sxs-lookup"><span data-stu-id="87385-234">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="87385-235">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; é disparado quando o host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="87385-235">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="87385-236">`StopAsync` contém a lógica para encerrar a tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-236">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="87385-237">Implemente o <xref:System.IDisposable> e os [finalizadores (destruidores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para descartar todos os recursos não gerenciados.</span><span class="sxs-lookup"><span data-stu-id="87385-237">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="87385-238">O token de cancelamento tem um tempo limite padrão de cinco segundos para indicar que o processo de desligamento não deve mais ser normal.</span><span class="sxs-lookup"><span data-stu-id="87385-238">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="87385-239">Quando for solicitado um cancelamento no token:</span><span class="sxs-lookup"><span data-stu-id="87385-239">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="87385-240">Todas as demais operações em segundo plano que o aplicativo estiver executando deverão ser anuladas.</span><span class="sxs-lookup"><span data-stu-id="87385-240">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="87385-241">Todos os métodos chamados em `StopAsync` deverão retornar imediatamente.</span><span class="sxs-lookup"><span data-stu-id="87385-241">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="87385-242">No entanto, as tarefas não são abandonadas após a solicitação de cancelamento&mdash;o chamador aguarda a conclusão de todas as tarefas.</span><span class="sxs-lookup"><span data-stu-id="87385-242">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="87385-243">Se o aplicativo for desligado inesperadamente (por exemplo, em uma falha do processo do aplicativo), `StopAsync` não poderá ser chamado.</span><span class="sxs-lookup"><span data-stu-id="87385-243">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="87385-244">Portanto, os métodos chamados ou operações realizadas em `StopAsync` talvez não ocorram.</span><span class="sxs-lookup"><span data-stu-id="87385-244">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="87385-245">Para estender o tempo limite de desligamento padrão de cinco segundos, defina:</span><span class="sxs-lookup"><span data-stu-id="87385-245">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="87385-246"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> ao usar o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="87385-246"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="87385-247">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="87385-247">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="87385-248">Definição da configuração de host de tempo limite de desligamento ao usar o Host da Web.</span><span class="sxs-lookup"><span data-stu-id="87385-248">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="87385-249">Para obter mais informações, consulte <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="87385-249">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="87385-250">O serviço hospedado é ativado uma única vez na inicialização do aplicativo e desligado normalmente durante o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="87385-250">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="87385-251">Se um erro for gerado durante a execução da tarefa em segundo plano, `Dispose` deverá ser chamado mesmo se `StopAsync` não for chamado.</span><span class="sxs-lookup"><span data-stu-id="87385-251">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="87385-252">Tarefas em segundo plano temporizadas</span><span class="sxs-lookup"><span data-stu-id="87385-252">Timed background tasks</span></span>

<span data-ttu-id="87385-253">Uma tarefa em segundo plano temporizada usa a classe [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="87385-253">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="87385-254">O temporizador dispara o método `DoWork` da tarefa.</span><span class="sxs-lookup"><span data-stu-id="87385-254">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="87385-255">O temporizador é desabilitado em `StopAsync` e descartado quando o contêiner de serviço é descartado em `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="87385-255">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="87385-256">O serviço está registrado em `Startup.ConfigureServices` com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="87385-256">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="87385-257">Consumindo um serviço com escopo em uma tarefa em segundo plano</span><span class="sxs-lookup"><span data-stu-id="87385-257">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="87385-258">Para usar os [serviços com escopo](xref:fundamentals/dependency-injection#service-lifetimes) dentro de um `IHostedService`, crie um escopo.</span><span class="sxs-lookup"><span data-stu-id="87385-258">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="87385-259">Por padrão, nenhum escopo é criado para um serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="87385-259">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="87385-260">O serviço da tarefa em segundo plano com escopo contém a lógica da tarefa em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="87385-260">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="87385-261">No exemplo a seguir, um <xref:Microsoft.Extensions.Logging.ILogger> é injetado no serviço:</span><span class="sxs-lookup"><span data-stu-id="87385-261">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="87385-262">O serviço hospedado cria um escopo para resolver o serviço da tarefa em segundo plano com escopo para chamar seu método `DoWork`:</span><span class="sxs-lookup"><span data-stu-id="87385-262">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="87385-263">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="87385-263">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="87385-264">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="87385-264">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="87385-265">Tarefas em segundo plano na fila</span><span class="sxs-lookup"><span data-stu-id="87385-265">Queued background tasks</span></span>

<span data-ttu-id="87385-266">Uma fila de tarefas em segundo plano é baseada no .NET 4 <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> . x ([provisoriamente agendado para ser interno para ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="87385-266">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="87385-267">No `QueueHostedService`, as tarefas em segundo plano na fila são removidas da fila e executadas como um <xref:Microsoft.Extensions.Hosting.BackgroundService>, que é uma classe base para implementar um `IHostedService` de longa execução:</span><span class="sxs-lookup"><span data-stu-id="87385-267">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="87385-268">Os serviços são registrados em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="87385-268">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="87385-269">A implementação `IHostedService` é registrada com o método de extensão `AddHostedService`:</span><span class="sxs-lookup"><span data-stu-id="87385-269">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="87385-270">Na classe de modelo de página de índice:</span><span class="sxs-lookup"><span data-stu-id="87385-270">In the Index page model class:</span></span>

* <span data-ttu-id="87385-271">O `IBackgroundTaskQueue` é injetado no construtor e atribuído a `Queue`.</span><span class="sxs-lookup"><span data-stu-id="87385-271">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="87385-272">Um <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> é injetado e atribuído a `_serviceScopeFactory`.</span><span class="sxs-lookup"><span data-stu-id="87385-272">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="87385-273">O alocador é usado para criar instâncias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que é usado para criar serviços dentro de um escopo.</span><span class="sxs-lookup"><span data-stu-id="87385-273">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="87385-274">Um escopo é criado para usar o aplicativo `AppDbContext` (um [serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes)) para gravar registros de banco de dados `IBackgroundTaskQueue` (um serviço singleton).</span><span class="sxs-lookup"><span data-stu-id="87385-274">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="87385-275">Quando o botão **Adicionar Tarefa** é selecionado na página de índice, o método `OnPostAddTask` é executado.</span><span class="sxs-lookup"><span data-stu-id="87385-275">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="87385-276">`QueueBackgroundWorkItem`é chamado para enfileirar um item de trabalho:</span><span class="sxs-lookup"><span data-stu-id="87385-276">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="87385-277">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="87385-277">Additional resources</span></span>

* [<span data-ttu-id="87385-278">Implementar tarefas em segundo plano em microsserviços com IHostedService e a classe BackgroundService</span><span class="sxs-lookup"><span data-stu-id="87385-278">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
