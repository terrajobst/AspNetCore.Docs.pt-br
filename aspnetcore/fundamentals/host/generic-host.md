---
title: Host Genérico .NET
author: tdykstra
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 9f5ecc7840fc7ffd9432a3bb67d0418efb7e8fd6
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975626"
---
# <a name="net-generic-host"></a><span data-ttu-id="2f97b-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="2f97b-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2f97b-104">Este artigo apresenta o Host Genérico do .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) e fornece diretrizes sobre como usá-lo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="2f97b-105">O que é um host?</span><span class="sxs-lookup"><span data-stu-id="2f97b-105">What's a host?</span></span>

<span data-ttu-id="2f97b-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="2f97b-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="2f97b-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="2f97b-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="2f97b-108">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="2f97b-108">Logging</span></span>
* <span data-ttu-id="2f97b-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="2f97b-109">Configuration</span></span>
* <span data-ttu-id="2f97b-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="2f97b-110">`IHostedService` implementations</span></span>

<span data-ttu-id="2f97b-111">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="2f97b-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="2f97b-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="2f97b-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="2f97b-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2f97b-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="2f97b-114">Em versões do ASP.NET Core anteriores à 3.0, o [host da Web](xref:fundamentals/host/web-host) é usado para cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="2f97b-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="2f97b-115">O host da Web não é mais recomendado para aplicativos Web e permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="2f97b-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="2f97b-116">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="2f97b-116">Set up a host</span></span>

<span data-ttu-id="2f97b-117">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="2f97b-118">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-118">The `Main` method:</span></span>

* <span data-ttu-id="2f97b-119">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="2f97b-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="2f97b-120">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="2f97b-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="2f97b-121">Eis aqui o código *Program.cs* para uma carga de trabalho não HTTP, com uma única implementação de `IHostedService` adicionada ao contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="2f97b-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="2f97b-122">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="2f97b-123">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="2f97b-124">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="2f97b-125">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="2f97b-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="2f97b-126">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="2f97b-126">Default builder settings</span></span> 

<span data-ttu-id="2f97b-127">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="2f97b-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="2f97b-128">Define a raiz do conteúdo como o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-128">Sets the content root to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="2f97b-129">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="2f97b-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="2f97b-130">Variáveis de ambiente prefixadas com "DOTNET_".</span><span class="sxs-lookup"><span data-stu-id="2f97b-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="2f97b-131">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2f97b-131">Command-line arguments.</span></span>
* <span data-ttu-id="2f97b-132">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="2f97b-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="2f97b-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2f97b-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="2f97b-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2f97b-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="2f97b-135">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="2f97b-136">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f97b-136">Environment variables.</span></span>
  * <span data-ttu-id="2f97b-137">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2f97b-137">Command-line arguments.</span></span>
* <span data-ttu-id="2f97b-138">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="2f97b-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="2f97b-139">Console</span><span class="sxs-lookup"><span data-stu-id="2f97b-139">Console</span></span>
  * <span data-ttu-id="2f97b-140">Depurar</span><span class="sxs-lookup"><span data-stu-id="2f97b-140">Debug</span></span>
  * <span data-ttu-id="2f97b-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="2f97b-141">EventSource</span></span>
  * <span data-ttu-id="2f97b-142">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="2f97b-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="2f97b-143">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2f97b-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="2f97b-144">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="2f97b-145">Carrega a configuração do host de variáveis de ambiente prefixadas com "ASPNETCORE_".</span><span class="sxs-lookup"><span data-stu-id="2f97b-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="2f97b-146">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="2f97b-147">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="2f97b-148">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2f97b-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="2f97b-149">Adiciona [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.</span><span class="sxs-lookup"><span data-stu-id="2f97b-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="2f97b-150">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="2f97b-150">Enables IIS integration.</span></span> <span data-ttu-id="2f97b-151">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="2f97b-152">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="2f97b-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="2f97b-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="2f97b-153">Framework-provided services</span></span>

<span data-ttu-id="2f97b-154">Serviços que são registradosautomaticamente incluem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2f97b-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="2f97b-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2f97b-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="2f97b-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2f97b-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="2f97b-157">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2f97b-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="2f97b-158">Para obter uma lista de todos os serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-158">For a list of all framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="2f97b-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2f97b-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="2f97b-160">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2f97b-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="2f97b-161">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="2f97b-162">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="2f97b-163">O exemplo a seguir é uma implementação `IHostedService` que registra os eventos `IApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-163">The following example is an `IHostedService` implementation that registers the `IApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="2f97b-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="2f97b-164">IHostLifetime</span></span>

<span data-ttu-id="2f97b-165">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="2f97b-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="2f97b-166">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="2f97b-166">The last implementation registered is used.</span></span>

<span data-ttu-id="2f97b-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="2f97b-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="2f97b-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="2f97b-169">escuta Ctrl + C/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="2f97b-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="2f97b-170">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="2f97b-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="2f97b-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="2f97b-171">IHostEnvironment</span></span>

<span data-ttu-id="2f97b-172">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostEnvironment> em uma classe para obter informações sobre o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2f97b-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="2f97b-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2f97b-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="2f97b-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2f97b-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="2f97b-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="2f97b-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="2f97b-176">Os aplicativos Web implementam a interface `IWebHostEnvironment`, que herda `IHostEnvironment` e adiciona:</span><span class="sxs-lookup"><span data-stu-id="2f97b-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="2f97b-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="2f97b-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="2f97b-178">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="2f97b-178">Host configuration</span></span>

<span data-ttu-id="2f97b-179">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="2f97b-180">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="2f97b-181">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="2f97b-182">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="2f97b-183">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2f97b-184">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2f97b-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="2f97b-185">O provedor de variável de ambiente com o prefixo `DOTNET_` e os argumentos de linha de comando são incluídos pelo CreateDefaultBuilder.</span><span class="sxs-lookup"><span data-stu-id="2f97b-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="2f97b-186">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="2f97b-187">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="2f97b-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="2f97b-188">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="2f97b-189">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="2f97b-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="2f97b-190">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2f97b-190">App configuration</span></span>

<span data-ttu-id="2f97b-191">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="2f97b-192">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="2f97b-193">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2f97b-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="2f97b-194">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="2f97b-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="2f97b-195">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="2f97b-196">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="2f97b-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="2f97b-197">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="2f97b-197">Settings for all app types</span></span>

<span data-ttu-id="2f97b-198">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="2f97b-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="2f97b-199">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

### <a name="applicationname"></a><span data-ttu-id="2f97b-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="2f97b-200">ApplicationName</span></span>

<span data-ttu-id="2f97b-201">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="2f97b-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="2f97b-202">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="2f97b-202">**Key**: applicationName</span></span>  
<span data-ttu-id="2f97b-203">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-203">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-204">**Padrão**: O nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="2f97b-205">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="2f97b-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="2f97b-206">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f97b-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="2f97b-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="2f97b-207">ContentRootPath</span></span>

<span data-ttu-id="2f97b-208">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="2f97b-209">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="2f97b-210">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="2f97b-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="2f97b-211">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-211">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-212">**Padrão**: A pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="2f97b-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="2f97b-213">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="2f97b-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="2f97b-214">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a><span data-ttu-id="2f97b-215">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="2f97b-215">EnvironmentName</span></span>

<span data-ttu-id="2f97b-216">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="2f97b-216">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="2f97b-217">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="2f97b-218">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2f97b-218">Values aren't case sensitive.</span></span>

<span data-ttu-id="2f97b-219">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="2f97b-219">**Key**: environment</span></span>  
<span data-ttu-id="2f97b-220">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-220">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-221">**Padrão**: Produção</span><span class="sxs-lookup"><span data-stu-id="2f97b-221">**Default**: Production</span></span>  
<span data-ttu-id="2f97b-222">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="2f97b-222">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="2f97b-223">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-223">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="2f97b-224">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="2f97b-224">ShutdownTimeout</span></span>

<span data-ttu-id="2f97b-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="2f97b-226">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-226">The default value is five seconds.</span></span>  <span data-ttu-id="2f97b-227">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="2f97b-227">During the timeout period, the host:</span></span>

* <span data-ttu-id="2f97b-228">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="2f97b-228">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="2f97b-229">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="2f97b-229">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="2f97b-230">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-230">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="2f97b-231">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="2f97b-231">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="2f97b-232">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="2f97b-232">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="2f97b-233">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="2f97b-233">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="2f97b-234">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="2f97b-234">**Type**: *int*</span></span>  
<span data-ttu-id="2f97b-235">**Padrão**: **Variável de ambiente** de cinco segundos: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="2f97b-235">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="2f97b-236">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-236">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="2f97b-237">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="2f97b-237">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="2f97b-238">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="2f97b-238">Settings for web apps</span></span>

<span data-ttu-id="2f97b-239">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="2f97b-239">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="2f97b-240">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-240">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="2f97b-241">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="2f97b-241">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="2f97b-242">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2f97b-242">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="2f97b-243">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="2f97b-243">CaptureStartupErrors</span></span>

<span data-ttu-id="2f97b-244">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="2f97b-244">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="2f97b-245">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="2f97b-245">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="2f97b-246">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="2f97b-246">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="2f97b-247">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2f97b-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2f97b-248">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-248">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="2f97b-249">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="2f97b-249">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="2f97b-250">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-250">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="2f97b-251">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="2f97b-251">DetailedErrors</span></span>

<span data-ttu-id="2f97b-252">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="2f97b-252">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="2f97b-253">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="2f97b-253">**Key**: detailedErrors</span></span>  
<span data-ttu-id="2f97b-254">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2f97b-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2f97b-255">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="2f97b-255">**Default**: false</span></span>  
<span data-ttu-id="2f97b-256">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="2f97b-256">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="2f97b-257">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-257">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="2f97b-258">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="2f97b-258">HostingStartupAssemblies</span></span>

<span data-ttu-id="2f97b-259">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2f97b-259">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="2f97b-260">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-260">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="2f97b-261">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="2f97b-261">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="2f97b-262">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="2f97b-262">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="2f97b-263">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-263">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-264">**Padrão**: Cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="2f97b-264">**Default**: Empty string</span></span>  
<span data-ttu-id="2f97b-265">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2f97b-265">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="2f97b-266">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-266">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="2f97b-267">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="2f97b-267">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="2f97b-268">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2f97b-268">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="2f97b-269">**Chave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="2f97b-269">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="2f97b-270">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-270">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-271">**Padrão**: Cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="2f97b-271">**Default**: Empty string</span></span>  
<span data-ttu-id="2f97b-272">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="2f97b-272">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="2f97b-273">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="2f97b-274">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="2f97b-274">HTTPS_Port</span></span>

<span data-ttu-id="2f97b-275">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2f97b-275">The HTTPS redirect port.</span></span> <span data-ttu-id="2f97b-276">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2f97b-276">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2f97b-277">**Chave**: https_port **Tipo**: *cadeia de caracteres*
**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="2f97b-277">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="2f97b-278">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="2f97b-278">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="2f97b-279">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="2f97b-280">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="2f97b-280">PreferHostingUrls</span></span>

<span data-ttu-id="2f97b-281">Indica se o host deve escutar as URLs configuradas com o `IWebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-281">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="2f97b-282">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="2f97b-282">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="2f97b-283">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2f97b-283">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2f97b-284">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="2f97b-284">**Default**: true</span></span>  
<span data-ttu-id="2f97b-285">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="2f97b-285">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="2f97b-286">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-286">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="2f97b-287">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="2f97b-287">PreventHostingStartup</span></span>

<span data-ttu-id="2f97b-288">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-288">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="2f97b-289">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-289">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="2f97b-290">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="2f97b-290">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="2f97b-291">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="2f97b-291">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="2f97b-292">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="2f97b-292">**Default**: false</span></span>  
<span data-ttu-id="2f97b-293">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="2f97b-293">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="2f97b-294">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-294">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="2f97b-295">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="2f97b-295">StartupAssembly</span></span>

<span data-ttu-id="2f97b-296">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-296">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="2f97b-297">**Chave**: startupAssembly **Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-297">**Key**: startupAssembly **Type**: *string*</span></span>  
<span data-ttu-id="2f97b-298">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2f97b-298">**Default**: The app's assembly</span></span>  
<span data-ttu-id="2f97b-299">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="2f97b-299">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="2f97b-300">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-300">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="2f97b-301">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="2f97b-301">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="2f97b-302">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="2f97b-302">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="2f97b-303">URLs</span><span class="sxs-lookup"><span data-stu-id="2f97b-303">URLs</span></span>

<span data-ttu-id="2f97b-304">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="2f97b-304">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="2f97b-305">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-305">For example, `http://localhost:123`.</span></span> <span data-ttu-id="2f97b-306">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="2f97b-306">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="2f97b-307">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="2f97b-307">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="2f97b-308">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="2f97b-308">Supported formats vary among servers.</span></span>

<span data-ttu-id="2f97b-309">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="2f97b-309">**Key**: urls</span></span>  
<span data-ttu-id="2f97b-310">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-310">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-311">**Padrão**: `http://localhost:5000` e `https://localhost:5001`
**variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="2f97b-311">**Default**: `http://localhost:5000` and `https://localhost:5001`
**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="2f97b-312">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-312">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="2f97b-313">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="2f97b-313">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="2f97b-314">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-314">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="2f97b-315">WebRoot</span><span class="sxs-lookup"><span data-stu-id="2f97b-315">WebRoot</span></span>

<span data-ttu-id="2f97b-316">O caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-316">The relative path to the app's static assets.</span></span>

<span data-ttu-id="2f97b-317">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="2f97b-317">**Key**: webroot</span></span>  
<span data-ttu-id="2f97b-318">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-318">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-319">**Padrão**: *(Raiz de conteúdo)/wwwroot* se o caminho existir.</span><span class="sxs-lookup"><span data-stu-id="2f97b-319">**Default**: *(Content Root)/wwwroot*, if the path exists.</span></span> <span data-ttu-id="2f97b-320">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-320">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="2f97b-321">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="2f97b-321">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="2f97b-322">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-322">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="2f97b-323">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="2f97b-323">Manage the host lifetime</span></span>

<span data-ttu-id="2f97b-324">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-324">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="2f97b-325">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="2f97b-325">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="2f97b-326">Executar</span><span class="sxs-lookup"><span data-stu-id="2f97b-326">Run</span></span>

<span data-ttu-id="2f97b-327"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-327"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="2f97b-328">RunAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-328">RunAsync</span></span>

<span data-ttu-id="2f97b-329"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-329"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="2f97b-330">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-330">RunConsoleAsync</span></span>

<span data-ttu-id="2f97b-331"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda Ctrl + C/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="2f97b-331"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="2f97b-332">Início</span><span class="sxs-lookup"><span data-stu-id="2f97b-332">Start</span></span>

<span data-ttu-id="2f97b-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="2f97b-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="2f97b-334">StartAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-334">StartAsync</span></span>

<span data-ttu-id="2f97b-335"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-335"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="2f97b-336"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="2f97b-336"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="2f97b-337">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-337">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="2f97b-338">StopAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-338">StopAsync</span></span>

<span data-ttu-id="2f97b-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="2f97b-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="2f97b-340">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="2f97b-340">WaitForShutdown</span></span>

<span data-ttu-id="2f97b-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloqueia o thread de chamada até que o desligamento seja disparado por IHostLifetime, por exemplo, por meio de Ctrl + C/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="2f97b-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="2f97b-342">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-342">WaitForShutdownAsync</span></span>

<span data-ttu-id="2f97b-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="2f97b-344">Controle externo</span><span class="sxs-lookup"><span data-stu-id="2f97b-344">External control</span></span>

<span data-ttu-id="2f97b-345">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="2f97b-345">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="2f97b-346">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="2f97b-346">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="2f97b-347">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-347">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="2f97b-348">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="2f97b-348">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="2f97b-349">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="2f97b-349">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="2f97b-350">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="2f97b-350">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="2f97b-351">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="2f97b-351">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="2f97b-352">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="2f97b-352">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2f97b-353">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="2f97b-353">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="2f97b-354">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f97b-354">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2f97b-355">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="2f97b-355">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="2f97b-356">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-356">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="2f97b-357">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="2f97b-357">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="2f97b-358">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="2f97b-358">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="2f97b-359">Na configuração **Inicialização do .NET Core (console)** , localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="2f97b-359">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="2f97b-360">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-360">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="2f97b-361">Introdução</span><span class="sxs-lookup"><span data-stu-id="2f97b-361">Introduction</span></span>

<span data-ttu-id="2f97b-362">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="2f97b-362">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="2f97b-363">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="2f97b-363">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="2f97b-364"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="2f97b-364"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="2f97b-365">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="2f97b-365">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="2f97b-366"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="2f97b-366"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="2f97b-367">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="2f97b-367">Set up a host</span></span>

<span data-ttu-id="2f97b-368"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="2f97b-368"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="2f97b-369">Opções</span><span class="sxs-lookup"><span data-stu-id="2f97b-369">Options</span></span>

<span data-ttu-id="2f97b-370">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-370"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="2f97b-371">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="2f97b-371">Shutdown timeout</span></span>

<span data-ttu-id="2f97b-372">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-372"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="2f97b-373">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-373">The default value is five seconds.</span></span>

<span data-ttu-id="2f97b-374">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="2f97b-374">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="2f97b-375">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="2f97b-375">Default services</span></span>

<span data-ttu-id="2f97b-376">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="2f97b-376">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="2f97b-377">[Ambiente](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="2f97b-377">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="2f97b-378">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="2f97b-378">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="2f97b-379"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="2f97b-379"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="2f97b-380"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="2f97b-380"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="2f97b-381">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="2f97b-381">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="2f97b-382">[Registro em log](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="2f97b-382">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="2f97b-383">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="2f97b-383">Host configuration</span></span>

<span data-ttu-id="2f97b-384">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="2f97b-384">Host configuration is created by:</span></span>

* <span data-ttu-id="2f97b-385">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="2f97b-385">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="2f97b-386">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-386">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="2f97b-387">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="2f97b-387">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="2f97b-388">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="2f97b-388">Application key (name)</span></span>

<span data-ttu-id="2f97b-389">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="2f97b-389">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="2f97b-390">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="2f97b-390">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="2f97b-391">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="2f97b-391">**Key**: applicationName</span></span>  
<span data-ttu-id="2f97b-392">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-392">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-393">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-393">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="2f97b-394">**Definido usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="2f97b-394">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="2f97b-395">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2f97b-395">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="2f97b-396">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="2f97b-396">Content root</span></span>

<span data-ttu-id="2f97b-397">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-397">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="2f97b-398">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="2f97b-398">**Key**: contentRoot</span></span>  
<span data-ttu-id="2f97b-399">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-399">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-400">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="2f97b-400">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="2f97b-401">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="2f97b-401">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="2f97b-402">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2f97b-402">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="2f97b-403">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-403">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="2f97b-404">Ambiente</span><span class="sxs-lookup"><span data-stu-id="2f97b-404">Environment</span></span>

<span data-ttu-id="2f97b-405">Define o [ambiente](xref:fundamentals/environments) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-405">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="2f97b-406">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="2f97b-406">**Key**: environment</span></span>  
<span data-ttu-id="2f97b-407">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="2f97b-407">**Type**: *string*</span></span>  
<span data-ttu-id="2f97b-408">**Padrão**: Produção</span><span class="sxs-lookup"><span data-stu-id="2f97b-408">**Default**: Production</span></span>  
<span data-ttu-id="2f97b-409">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="2f97b-409">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="2f97b-410">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="2f97b-410">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="2f97b-411">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="2f97b-411">The environment can be set to any value.</span></span> <span data-ttu-id="2f97b-412">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-412">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="2f97b-413">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2f97b-413">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="2f97b-414">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="2f97b-414">ConfigureHostConfiguration</span></span>

<span data-ttu-id="2f97b-415"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="2f97b-415"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="2f97b-416">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-416">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="2f97b-417"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-417"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="2f97b-418">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2f97b-418">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="2f97b-419">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="2f97b-419">No providers are included by default.</span></span> <span data-ttu-id="2f97b-420">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="2f97b-420">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="2f97b-421">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="2f97b-421">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="2f97b-422">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2f97b-422">Environment variable configuration.</span></span>
* <span data-ttu-id="2f97b-423">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2f97b-423">Command-line argument configuration.</span></span>
* <span data-ttu-id="2f97b-424">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="2f97b-424">Any other required configuration providers.</span></span>

<span data-ttu-id="2f97b-425">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2f97b-425">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="2f97b-426">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-426">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="2f97b-427">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="2f97b-427">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="2f97b-428">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="2f97b-428">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="2f97b-429">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-429">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="2f97b-430">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="2f97b-430">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="2f97b-431">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="2f97b-431">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="2f97b-432">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2f97b-432">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="2f97b-433">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2f97b-433">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="2f97b-434">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-434">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2f97b-435">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-435">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="2f97b-436">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="2f97b-436">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="2f97b-437">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2f97b-437">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="2f97b-438">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="2f97b-438">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="2f97b-439">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="2f97b-439">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="2f97b-440">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2f97b-440">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2f97b-441">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-441">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="2f97b-442"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-442"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="2f97b-443"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-443"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="2f97b-444">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="2f97b-444">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="2f97b-445">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-445">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="2f97b-446">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="2f97b-446">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="2f97b-447">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="2f97b-447">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="2f97b-448">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2f97b-448">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="2f97b-449">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="2f97b-449">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="2f97b-450">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="2f97b-450">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="2f97b-451">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="2f97b-451">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="2f97b-452">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-452">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="2f97b-453">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="2f97b-453">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="2f97b-454">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="2f97b-454">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="2f97b-455">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-455">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="2f97b-456">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="2f97b-456">ConfigureServices</span></span>

<span data-ttu-id="2f97b-457"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-457"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2f97b-458"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-458"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="2f97b-459">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-459">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="2f97b-460">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-460">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="2f97b-461">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2f97b-461">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="2f97b-462">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="2f97b-462">ConfigureLogging</span></span>

<span data-ttu-id="2f97b-463"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="2f97b-463"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="2f97b-464"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-464"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="2f97b-465">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="2f97b-465">UseConsoleLifetime</span></span>

<span data-ttu-id="2f97b-466"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escuta Ctrl + C/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="2f97b-466"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="2f97b-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="2f97b-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="2f97b-468"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="2f97b-468"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="2f97b-469">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="2f97b-469">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="2f97b-470">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="2f97b-470">Container configuration</span></span>

<span data-ttu-id="2f97b-471">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-471">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="2f97b-472">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="2f97b-472">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="2f97b-473">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-473">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="2f97b-474">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-474">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="2f97b-475"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="2f97b-475"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="2f97b-476"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-476"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="2f97b-477">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2f97b-477">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="2f97b-478">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="2f97b-478">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="2f97b-479">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2f97b-479">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="2f97b-480">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="2f97b-480">Extensibility</span></span>

<span data-ttu-id="2f97b-481">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-481">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="2f97b-482">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-482">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="2f97b-483">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="2f97b-483">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="2f97b-484">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="2f97b-484">Manage the host</span></span>

<span data-ttu-id="2f97b-485">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="2f97b-485">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="2f97b-486">Executar</span><span class="sxs-lookup"><span data-stu-id="2f97b-486">Run</span></span>

<span data-ttu-id="2f97b-487"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="2f97b-487"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="2f97b-488">RunAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-488">RunAsync</span></span>

<span data-ttu-id="2f97b-489"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="2f97b-489"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="2f97b-490">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-490">RunConsoleAsync</span></span>

<span data-ttu-id="2f97b-491"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda Ctrl + C/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="2f97b-491"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="2f97b-492">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-492">Start and StopAsync</span></span>

<span data-ttu-id="2f97b-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="2f97b-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="2f97b-494"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="2f97b-494"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="2f97b-495">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-495">StartAsync and StopAsync</span></span>

<span data-ttu-id="2f97b-496"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-496"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="2f97b-497"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-497"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="2f97b-498">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="2f97b-498">WaitForShutdown</span></span>

<span data-ttu-id="2f97b-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> é disparado por meio de <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (escuta Ctrl + C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="2f97b-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="2f97b-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="2f97b-501">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="2f97b-501">WaitForShutdownAsync</span></span>

<span data-ttu-id="2f97b-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="2f97b-503">Controle externo</span><span class="sxs-lookup"><span data-stu-id="2f97b-503">External control</span></span>

<span data-ttu-id="2f97b-504">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="2f97b-504">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="2f97b-505"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="2f97b-505"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="2f97b-506">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-506">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="2f97b-507">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="2f97b-507">IHostingEnvironment interface</span></span>

<span data-ttu-id="2f97b-508"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-508"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="2f97b-509">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="2f97b-509">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="2f97b-510">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2f97b-510">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="2f97b-511">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="2f97b-511">IApplicationLifetime interface</span></span>

<span data-ttu-id="2f97b-512"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2f97b-512"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="2f97b-513">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="2f97b-513">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="2f97b-514">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="2f97b-514">Cancellation Token</span></span> | <span data-ttu-id="2f97b-515">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="2f97b-515">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="2f97b-516">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="2f97b-516">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="2f97b-517">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2f97b-517">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="2f97b-518">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="2f97b-518">All requests should be processed.</span></span> <span data-ttu-id="2f97b-519">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="2f97b-519">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="2f97b-520">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="2f97b-520">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="2f97b-521">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="2f97b-521">Requests may still be processing.</span></span> <span data-ttu-id="2f97b-522">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="2f97b-522">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="2f97b-523">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="2f97b-523">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="2f97b-524">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="2f97b-524">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="2f97b-525">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f97b-525">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="2f97b-526"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2f97b-526"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="2f97b-527">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="2f97b-527">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="2f97b-528">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2f97b-528">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
