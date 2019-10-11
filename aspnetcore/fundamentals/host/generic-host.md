---
title: Host Genérico .NET
author: rick-anderson
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 8e29c3a300cc1cdc37458427d3be7ceed84385ef
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259633"
---
# <a name="net-generic-host"></a><span data-ttu-id="2a9dd-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="2a9dd-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a9dd-104">Este artigo apresenta o Host Genérico do .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) e fornece diretrizes sobre como usá-lo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="2a9dd-105">O que é um host?</span><span class="sxs-lookup"><span data-stu-id="2a9dd-105">What's a host?</span></span>

<span data-ttu-id="2a9dd-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="2a9dd-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="2a9dd-108">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="2a9dd-108">Logging</span></span>
* <span data-ttu-id="2a9dd-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="2a9dd-109">Configuration</span></span>
* <span data-ttu-id="2a9dd-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-110">`IHostedService` implementations</span></span>

<span data-ttu-id="2a9dd-111">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="2a9dd-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="2a9dd-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="2a9dd-114">Em versões do ASP.NET Core anteriores à 3.0, o [host da Web](xref:fundamentals/host/web-host) é usado para cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="2a9dd-115">O host da Web não é mais recomendado para aplicativos Web e permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="2a9dd-116">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="2a9dd-116">Set up a host</span></span>

<span data-ttu-id="2a9dd-117">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="2a9dd-118">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-118">The `Main` method:</span></span>

* <span data-ttu-id="2a9dd-119">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="2a9dd-120">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="2a9dd-121">Eis aqui o código *Program.cs* para uma carga de trabalho não HTTP, com uma única implementação de `IHostedService` adicionada ao contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="2a9dd-122">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="2a9dd-123">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="2a9dd-124">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="2a9dd-125">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="2a9dd-126">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="2a9dd-126">Default builder settings</span></span> 

<span data-ttu-id="2a9dd-127">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="2a9dd-128">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="2a9dd-129">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="2a9dd-130">Variáveis de ambiente prefixadas com "DOTNET_".</span><span class="sxs-lookup"><span data-stu-id="2a9dd-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="2a9dd-131">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-131">Command-line arguments.</span></span>
* <span data-ttu-id="2a9dd-132">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="2a9dd-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="2a9dd-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="2a9dd-135">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="2a9dd-136">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-136">Environment variables.</span></span>
  * <span data-ttu-id="2a9dd-137">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-137">Command-line arguments.</span></span>
* <span data-ttu-id="2a9dd-138">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="2a9dd-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="2a9dd-139">Console</span><span class="sxs-lookup"><span data-stu-id="2a9dd-139">Console</span></span>
  * <span data-ttu-id="2a9dd-140">Depurar</span><span class="sxs-lookup"><span data-stu-id="2a9dd-140">Debug</span></span>
  * <span data-ttu-id="2a9dd-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="2a9dd-141">EventSource</span></span>
  * <span data-ttu-id="2a9dd-142">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="2a9dd-143">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="2a9dd-144">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="2a9dd-145">Carrega a configuração do host de variáveis de ambiente prefixadas com "ASPNETCORE_".</span><span class="sxs-lookup"><span data-stu-id="2a9dd-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="2a9dd-146">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="2a9dd-147">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="2a9dd-148">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="2a9dd-149">Adiciona [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="2a9dd-150">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-150">Enables IIS integration.</span></span> <span data-ttu-id="2a9dd-151">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="2a9dd-152">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="2a9dd-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="2a9dd-153">Framework-provided services</span></span>

<span data-ttu-id="2a9dd-154">Serviços que são registradosautomaticamente incluem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="2a9dd-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2a9dd-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="2a9dd-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2a9dd-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="2a9dd-157">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2a9dd-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="2a9dd-158">Para obter mais informações sobre os serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="2a9dd-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2a9dd-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="2a9dd-160">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="2a9dd-161">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="2a9dd-162">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="2a9dd-163">O exemplo a seguir é uma implementação `IHostedService` que registra os eventos `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="2a9dd-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2a9dd-164">IHostLifetime</span></span>

<span data-ttu-id="2a9dd-165">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="2a9dd-166">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-166">The last implementation registered is used.</span></span>

<span data-ttu-id="2a9dd-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="2a9dd-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="2a9dd-169">escuta Ctrl + C/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="2a9dd-170">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="2a9dd-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2a9dd-171">IHostEnvironment</span></span>

<span data-ttu-id="2a9dd-172">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostEnvironment> em uma classe para obter informações sobre o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="2a9dd-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2a9dd-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="2a9dd-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2a9dd-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="2a9dd-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="2a9dd-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="2a9dd-176">Os aplicativos Web implementam a interface `IWebHostEnvironment`, que herda `IHostEnvironment` e adiciona:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="2a9dd-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="2a9dd-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="2a9dd-178">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="2a9dd-178">Host configuration</span></span>

<span data-ttu-id="2a9dd-179">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="2a9dd-180">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="2a9dd-181">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="2a9dd-182">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="2a9dd-183">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2a9dd-184">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="2a9dd-185">O provedor de variável de ambiente com o prefixo `DOTNET_` e os argumentos de linha de comando são incluídos pelo CreateDefaultBuilder.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="2a9dd-186">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="2a9dd-187">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="2a9dd-188">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="2a9dd-189">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="2a9dd-190">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2a9dd-190">App configuration</span></span>

<span data-ttu-id="2a9dd-191">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="2a9dd-192">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2a9dd-193">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="2a9dd-194">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="2a9dd-195">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="2a9dd-196">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="2a9dd-197">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="2a9dd-197">Settings for all app types</span></span>

<span data-ttu-id="2a9dd-198">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="2a9dd-199">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="2a9dd-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2a9dd-200">ApplicationName</span></span>

<span data-ttu-id="2a9dd-201">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="2a9dd-202">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="2a9dd-202">**Key**: applicationName</span></span>  
<span data-ttu-id="2a9dd-203">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-203">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-204">**Padrão**: O nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="2a9dd-205">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="2a9dd-206">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="2a9dd-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="2a9dd-207">ContentRootPath</span></span>

<span data-ttu-id="2a9dd-208">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="2a9dd-209">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="2a9dd-210">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="2a9dd-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="2a9dd-211">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-211">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-212">**Padrão**: A pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="2a9dd-213">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="2a9dd-214">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="2a9dd-215">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-215">For more information, see:</span></span>

* <span data-ttu-id="2a9dd-216">[Fundamentals: Raiz do conteúdo @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="2a9dd-216">[Fundamentals: Content root](xref:fundamentals/index#content-root)</span></span>
* [<span data-ttu-id="2a9dd-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="2a9dd-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="2a9dd-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2a9dd-218">EnvironmentName</span></span>

<span data-ttu-id="2a9dd-219">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="2a9dd-220">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="2a9dd-221">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-221">Values aren't case sensitive.</span></span>

<span data-ttu-id="2a9dd-222">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="2a9dd-222">**Key**: environment</span></span>  
<span data-ttu-id="2a9dd-223">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-223">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-224">**Padrão**: Produção</span><span class="sxs-lookup"><span data-stu-id="2a9dd-224">**Default**: Production</span></span>  
<span data-ttu-id="2a9dd-225">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="2a9dd-226">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="2a9dd-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="2a9dd-227">ShutdownTimeout</span></span>

<span data-ttu-id="2a9dd-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="2a9dd-229">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-229">The default value is five seconds.</span></span>  <span data-ttu-id="2a9dd-230">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="2a9dd-231">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="2a9dd-232">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="2a9dd-233">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="2a9dd-234">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="2a9dd-235">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="2a9dd-236">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="2a9dd-236">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="2a9dd-237">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-237">**Type**: *int*</span></span>  
<span data-ttu-id="2a9dd-238">**Padrão**: **Variável de ambiente** de cinco segundos: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-238">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="2a9dd-239">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="2a9dd-240">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="2a9dd-241">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="2a9dd-241">Settings for web apps</span></span>

<span data-ttu-id="2a9dd-242">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-242">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="2a9dd-243">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-243">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="2a9dd-244">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-244">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="2a9dd-245">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-245">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="2a9dd-246">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="2a9dd-246">CaptureStartupErrors</span></span>

<span data-ttu-id="2a9dd-247">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-247">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="2a9dd-248">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-248">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="2a9dd-249">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="2a9dd-249">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="2a9dd-250">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-250">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2a9dd-251">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-251">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="2a9dd-252">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-252">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="2a9dd-253">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-253">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="2a9dd-254">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="2a9dd-254">DetailedErrors</span></span>

<span data-ttu-id="2a9dd-255">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-255">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="2a9dd-256">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="2a9dd-256">**Key**: detailedErrors</span></span>  
<span data-ttu-id="2a9dd-257">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-257">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2a9dd-258">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="2a9dd-258">**Default**: false</span></span>  
<span data-ttu-id="2a9dd-259">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-259">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="2a9dd-260">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-260">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="2a9dd-261">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="2a9dd-261">HostingStartupAssemblies</span></span>

<span data-ttu-id="2a9dd-262">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-262">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="2a9dd-263">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-263">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="2a9dd-264">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-264">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="2a9dd-265">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="2a9dd-265">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="2a9dd-266">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-266">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-267">**Padrão**: Cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="2a9dd-267">**Default**: Empty string</span></span>  
<span data-ttu-id="2a9dd-268">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-268">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="2a9dd-269">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="2a9dd-270">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="2a9dd-270">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="2a9dd-271">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-271">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="2a9dd-272">**Chave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="2a9dd-272">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="2a9dd-273">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-273">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-274">**Padrão**: Cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="2a9dd-274">**Default**: Empty string</span></span>  
<span data-ttu-id="2a9dd-275">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-275">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="2a9dd-276">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-276">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="2a9dd-277">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="2a9dd-277">HTTPS_Port</span></span>

<span data-ttu-id="2a9dd-278">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-278">The HTTPS redirect port.</span></span> <span data-ttu-id="2a9dd-279">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-279">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2a9dd-280">**Chave**: https_port</span><span class="sxs-lookup"><span data-stu-id="2a9dd-280">**Key**: https_port</span></span>  
<span data-ttu-id="2a9dd-281">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-281">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-282">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-282">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="2a9dd-283">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-283">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="2a9dd-284">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-284">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="2a9dd-285">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="2a9dd-285">PreferHostingUrls</span></span>

<span data-ttu-id="2a9dd-286">Indica se o host deve escutar as URLs configuradas com o `IWebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-286">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="2a9dd-287">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="2a9dd-287">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="2a9dd-288">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-288">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2a9dd-289">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="2a9dd-289">**Default**: true</span></span>  
<span data-ttu-id="2a9dd-290">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-290">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="2a9dd-291">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-291">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="2a9dd-292">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="2a9dd-292">PreventHostingStartup</span></span>

<span data-ttu-id="2a9dd-293">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-293">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="2a9dd-294">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-294">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="2a9dd-295">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="2a9dd-295">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="2a9dd-296">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-296">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2a9dd-297">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="2a9dd-297">**Default**: false</span></span>  
<span data-ttu-id="2a9dd-298">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-298">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="2a9dd-299">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-299">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="2a9dd-300">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="2a9dd-300">StartupAssembly</span></span>

<span data-ttu-id="2a9dd-301">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-301">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="2a9dd-302">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="2a9dd-302">**Key**: startupAssembly</span></span>  
<span data-ttu-id="2a9dd-303">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-303">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-304">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2a9dd-304">**Default**: The app's assembly</span></span>  
<span data-ttu-id="2a9dd-305">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-305">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="2a9dd-306">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-306">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="2a9dd-307">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-307">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="2a9dd-308">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-308">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="2a9dd-309">URLs</span><span class="sxs-lookup"><span data-stu-id="2a9dd-309">URLs</span></span>

<span data-ttu-id="2a9dd-310">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-310">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="2a9dd-311">Por exemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-311">For example, `http://localhost:123`.</span></span> <span data-ttu-id="2a9dd-312">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-312">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="2a9dd-313">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-313">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="2a9dd-314">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-314">Supported formats vary among servers.</span></span>

<span data-ttu-id="2a9dd-315">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="2a9dd-315">**Key**: urls</span></span>  
<span data-ttu-id="2a9dd-316">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-316">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-317">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-317">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="2a9dd-318">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-318">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="2a9dd-319">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-319">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="2a9dd-320">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-320">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="2a9dd-321">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-321">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="2a9dd-322">WebRoot</span><span class="sxs-lookup"><span data-stu-id="2a9dd-322">WebRoot</span></span>

<span data-ttu-id="2a9dd-323">O caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-323">The relative path to the app's static assets.</span></span>

<span data-ttu-id="2a9dd-324">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="2a9dd-324">**Key**: webroot</span></span>  
<span data-ttu-id="2a9dd-325">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-325">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-326">**Padrão**: O padrão é `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="2a9dd-327">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-327">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="2a9dd-328">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-328">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="2a9dd-329">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-329">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="2a9dd-330">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-330">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="2a9dd-331">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-331">For more information, see:</span></span>

* <span data-ttu-id="2a9dd-332">[Fundamentals: Raiz da Web @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="2a9dd-332">[Fundamentals: Web root](xref:fundamentals/index#web-root)</span></span>
* [<span data-ttu-id="2a9dd-333">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="2a9dd-333">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="2a9dd-334">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="2a9dd-334">Manage the host lifetime</span></span>

<span data-ttu-id="2a9dd-335">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-335">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="2a9dd-336">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-336">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="2a9dd-337">Executar</span><span class="sxs-lookup"><span data-stu-id="2a9dd-337">Run</span></span>

<span data-ttu-id="2a9dd-338"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-338"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="2a9dd-339">RunAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-339">RunAsync</span></span>

<span data-ttu-id="2a9dd-340"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-340"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="2a9dd-341">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-341">RunConsoleAsync</span></span>

<span data-ttu-id="2a9dd-342"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda Ctrl + C/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-342"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="2a9dd-343">Início</span><span class="sxs-lookup"><span data-stu-id="2a9dd-343">Start</span></span>

<span data-ttu-id="2a9dd-344"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-344"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="2a9dd-345">StartAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-345">StartAsync</span></span>

<span data-ttu-id="2a9dd-346"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-346"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="2a9dd-347"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-347"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="2a9dd-348">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-348">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="2a9dd-349">StopAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-349">StopAsync</span></span>

<span data-ttu-id="2a9dd-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="2a9dd-351">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="2a9dd-351">WaitForShutdown</span></span>

<span data-ttu-id="2a9dd-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloqueia o thread de chamada até que o desligamento seja disparado por IHostLifetime, por exemplo, por meio de Ctrl + C/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="2a9dd-353">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-353">WaitForShutdownAsync</span></span>

<span data-ttu-id="2a9dd-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="2a9dd-355">Controle externo</span><span class="sxs-lookup"><span data-stu-id="2a9dd-355">External control</span></span>

<span data-ttu-id="2a9dd-356">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-356">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a9dd-357">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-357">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="2a9dd-358">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-358">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="2a9dd-359">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-359">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="2a9dd-360">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-360">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="2a9dd-361">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-361">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="2a9dd-362">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-362">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="2a9dd-363">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-363">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2a9dd-364">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-364">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="2a9dd-365">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a9dd-365">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2a9dd-366">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-366">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="2a9dd-367">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-367">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="2a9dd-368">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-368">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="2a9dd-369">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-369">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="2a9dd-370">Na configuração **Inicialização do .NET Core (console)** , localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-370">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="2a9dd-371">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-371">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="2a9dd-372">Introdução</span><span class="sxs-lookup"><span data-stu-id="2a9dd-372">Introduction</span></span>

<span data-ttu-id="2a9dd-373">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-373">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="2a9dd-374">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-374">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="2a9dd-375"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-375"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="2a9dd-376">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-376">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="2a9dd-377"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-377"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="2a9dd-378">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="2a9dd-378">Set up a host</span></span>

<span data-ttu-id="2a9dd-379"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-379"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="2a9dd-380">Opções</span><span class="sxs-lookup"><span data-stu-id="2a9dd-380">Options</span></span>

<span data-ttu-id="2a9dd-381">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-381"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="2a9dd-382">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="2a9dd-382">Shutdown timeout</span></span>

<span data-ttu-id="2a9dd-383">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-383"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="2a9dd-384">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-384">The default value is five seconds.</span></span>

<span data-ttu-id="2a9dd-385">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-385">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="2a9dd-386">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="2a9dd-386">Default services</span></span>

<span data-ttu-id="2a9dd-387">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-387">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="2a9dd-388">[Ambiente](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-388">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="2a9dd-389">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-389">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="2a9dd-390"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-390"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="2a9dd-391"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-391"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="2a9dd-392">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-392">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="2a9dd-393">[Registro em log](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-393">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="2a9dd-394">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="2a9dd-394">Host configuration</span></span>

<span data-ttu-id="2a9dd-395">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-395">Host configuration is created by:</span></span>

* <span data-ttu-id="2a9dd-396">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-396">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="2a9dd-397">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-397">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="2a9dd-398">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="2a9dd-398">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="2a9dd-399">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="2a9dd-399">Application key (name)</span></span>

<span data-ttu-id="2a9dd-400">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-400">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="2a9dd-401">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="2a9dd-401">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="2a9dd-402">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="2a9dd-402">**Key**: applicationName</span></span>  
<span data-ttu-id="2a9dd-403">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-403">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-404">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-404">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="2a9dd-405">**Definido usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-405">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="2a9dd-406">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2a9dd-406">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="2a9dd-407">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="2a9dd-407">Content root</span></span>

<span data-ttu-id="2a9dd-408">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-408">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="2a9dd-409">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="2a9dd-409">**Key**: contentRoot</span></span>  
<span data-ttu-id="2a9dd-410">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-410">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-411">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-411">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="2a9dd-412">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-412">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="2a9dd-413">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2a9dd-413">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="2a9dd-414">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-414">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="2a9dd-415">Para obter mais informações, consulte [Fundamentals: Raiz do conteúdo @ no__t-0.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-415">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="2a9dd-416">Ambiente</span><span class="sxs-lookup"><span data-stu-id="2a9dd-416">Environment</span></span>

<span data-ttu-id="2a9dd-417">Define o [ambiente](xref:fundamentals/environments) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-417">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="2a9dd-418">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="2a9dd-418">**Key**: environment</span></span>  
<span data-ttu-id="2a9dd-419">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2a9dd-419">**Type**: *string*</span></span>  
<span data-ttu-id="2a9dd-420">**Padrão**: Produção</span><span class="sxs-lookup"><span data-stu-id="2a9dd-420">**Default**: Production</span></span>  
<span data-ttu-id="2a9dd-421">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="2a9dd-421">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="2a9dd-422">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2a9dd-422">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="2a9dd-423">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-423">The environment can be set to any value.</span></span> <span data-ttu-id="2a9dd-424">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-424">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="2a9dd-425">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-425">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="2a9dd-426">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="2a9dd-426">ConfigureHostConfiguration</span></span>

<span data-ttu-id="2a9dd-427"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-427"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="2a9dd-428">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-428">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="2a9dd-429"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-429"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="2a9dd-430">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-430">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="2a9dd-431">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-431">No providers are included by default.</span></span> <span data-ttu-id="2a9dd-432">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-432">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="2a9dd-433">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-433">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="2a9dd-434">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-434">Environment variable configuration.</span></span>
* <span data-ttu-id="2a9dd-435">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-435">Command-line argument configuration.</span></span>
* <span data-ttu-id="2a9dd-436">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-436">Any other required configuration providers.</span></span>

<span data-ttu-id="2a9dd-437">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-437">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="2a9dd-438">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-438">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="2a9dd-439">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-439">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="2a9dd-440">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-440">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="2a9dd-441">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-441">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="2a9dd-442">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-442">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="2a9dd-443">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-443">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="2a9dd-444">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-444">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="2a9dd-445">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-445">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="2a9dd-446">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-446">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2a9dd-447">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-447">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="2a9dd-448">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-448">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="2a9dd-449">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-449">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="2a9dd-450">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-450">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="2a9dd-451">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-451">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="2a9dd-452">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2a9dd-452">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2a9dd-453">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="2a9dd-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="2a9dd-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="2a9dd-456">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-456">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="2a9dd-457">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-457">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="2a9dd-458">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-458">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="2a9dd-459">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-459">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="2a9dd-460">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-460">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="2a9dd-461">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-461">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="2a9dd-462">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-462">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="2a9dd-463">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-463">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="2a9dd-464">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-464">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="2a9dd-465">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-465">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="2a9dd-466">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-466">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="2a9dd-467">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-467">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="2a9dd-468">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="2a9dd-468">ConfigureServices</span></span>

<span data-ttu-id="2a9dd-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2a9dd-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="2a9dd-471">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-471">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="2a9dd-472">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-472">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="2a9dd-473">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-473">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="2a9dd-474">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="2a9dd-474">ConfigureLogging</span></span>

<span data-ttu-id="2a9dd-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="2a9dd-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="2a9dd-477">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="2a9dd-477">UseConsoleLifetime</span></span>

<span data-ttu-id="2a9dd-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escuta Ctrl + C/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="2a9dd-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="2a9dd-480"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-480"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="2a9dd-481">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-481">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="2a9dd-482">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="2a9dd-482">Container configuration</span></span>

<span data-ttu-id="2a9dd-483">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-483">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="2a9dd-484">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-484">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="2a9dd-485">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-485">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="2a9dd-486">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-486">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="2a9dd-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="2a9dd-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="2a9dd-489">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-489">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="2a9dd-490">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-490">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="2a9dd-491">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-491">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="2a9dd-492">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="2a9dd-492">Extensibility</span></span>

<span data-ttu-id="2a9dd-493">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-493">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="2a9dd-494">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-494">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="2a9dd-495">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-495">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="2a9dd-496">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="2a9dd-496">Manage the host</span></span>

<span data-ttu-id="2a9dd-497">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-497">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="2a9dd-498">Executar</span><span class="sxs-lookup"><span data-stu-id="2a9dd-498">Run</span></span>

<span data-ttu-id="2a9dd-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="2a9dd-500">RunAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-500">RunAsync</span></span>

<span data-ttu-id="2a9dd-501"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-501"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="2a9dd-502">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-502">RunConsoleAsync</span></span>

<span data-ttu-id="2a9dd-503"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda Ctrl + C/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-503"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="2a9dd-504">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-504">Start and StopAsync</span></span>

<span data-ttu-id="2a9dd-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="2a9dd-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="2a9dd-507">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-507">StartAsync and StopAsync</span></span>

<span data-ttu-id="2a9dd-508"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-508"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="2a9dd-509"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-509"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="2a9dd-510">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="2a9dd-510">WaitForShutdown</span></span>

<span data-ttu-id="2a9dd-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> é disparado por meio de <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (escuta Ctrl + C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="2a9dd-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="2a9dd-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="2a9dd-513">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="2a9dd-513">WaitForShutdownAsync</span></span>

<span data-ttu-id="2a9dd-514"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-514"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="2a9dd-515">Controle externo</span><span class="sxs-lookup"><span data-stu-id="2a9dd-515">External control</span></span>

<span data-ttu-id="2a9dd-516">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-516">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="2a9dd-517"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-517"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="2a9dd-518">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-518">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="2a9dd-519">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="2a9dd-519">IHostingEnvironment interface</span></span>

<span data-ttu-id="2a9dd-520"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-520"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="2a9dd-521">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-521">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="2a9dd-522">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-522">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="2a9dd-523">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2a9dd-523">IApplicationLifetime interface</span></span>

<span data-ttu-id="2a9dd-524"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-524"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="2a9dd-525">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-525">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="2a9dd-526">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="2a9dd-526">Cancellation Token</span></span> | <span data-ttu-id="2a9dd-527">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="2a9dd-527">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="2a9dd-528">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-528">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="2a9dd-529">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-529">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="2a9dd-530">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-530">All requests should be processed.</span></span> <span data-ttu-id="2a9dd-531">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-531">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="2a9dd-532">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-532">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="2a9dd-533">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-533">Requests may still be processing.</span></span> <span data-ttu-id="2a9dd-534">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-534">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="2a9dd-535">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-535">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="2a9dd-536">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-536">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="2a9dd-537">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-537">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="2a9dd-538"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a9dd-538"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="2a9dd-539">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="2a9dd-539">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2a9dd-540">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2a9dd-540">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
