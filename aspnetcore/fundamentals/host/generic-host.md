---
title: Host Genérico .NET
author: rick-anderson
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/02/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 2ed4af109b5ccd303a03a0d9167649dda7793126
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717016"
---
# <a name="net-generic-host"></a><span data-ttu-id="cb125-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="cb125-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb125-104">Este artigo apresenta o Host Genérico do .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) e fornece diretrizes sobre como usá-lo.</span><span class="sxs-lookup"><span data-stu-id="cb125-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="cb125-105">O que é um host?</span><span class="sxs-lookup"><span data-stu-id="cb125-105">What's a host?</span></span>

<span data-ttu-id="cb125-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="cb125-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="cb125-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="cb125-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="cb125-108">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="cb125-108">Logging</span></span>
* <span data-ttu-id="cb125-109">Configuração do</span><span class="sxs-lookup"><span data-stu-id="cb125-109">Configuration</span></span>
* <span data-ttu-id="cb125-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="cb125-110">`IHostedService` implementations</span></span>

<span data-ttu-id="cb125-111">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="cb125-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="cb125-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="cb125-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="cb125-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="cb125-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="cb125-114">Em versões do ASP.NET Core anteriores à 3.0, o [host da Web](xref:fundamentals/host/web-host) é usado para cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb125-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="cb125-115">O host da Web não é mais recomendado para aplicativos Web e permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="cb125-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="cb125-116">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="cb125-116">Set up a host</span></span>

<span data-ttu-id="cb125-117">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="cb125-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="cb125-118">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="cb125-118">The `Main` method:</span></span>

* <span data-ttu-id="cb125-119">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="cb125-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="cb125-120">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="cb125-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="cb125-121">Eis aqui o código *Program.cs* para uma carga de trabalho não HTTP, com uma única implementação de `IHostedService` adicionada ao contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="cb125-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="cb125-122">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="cb125-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="cb125-123">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cb125-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="cb125-124">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="cb125-125">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="cb125-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="cb125-126">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="cb125-126">Default builder settings</span></span>

<span data-ttu-id="cb125-127">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="cb125-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="cb125-128">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="cb125-129">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="cb125-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="cb125-130">Variáveis de ambiente prefixadas com "DOTNET_".</span><span class="sxs-lookup"><span data-stu-id="cb125-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="cb125-131">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="cb125-131">Command-line arguments.</span></span>
* <span data-ttu-id="cb125-132">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="cb125-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="cb125-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cb125-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="cb125-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="cb125-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="cb125-135">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="cb125-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="cb125-136">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cb125-136">Environment variables.</span></span>
  * <span data-ttu-id="cb125-137">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="cb125-137">Command-line arguments.</span></span>
* <span data-ttu-id="cb125-138">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="cb125-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="cb125-139">Console do</span><span class="sxs-lookup"><span data-stu-id="cb125-139">Console</span></span>
  * <span data-ttu-id="cb125-140">Depuração</span><span class="sxs-lookup"><span data-stu-id="cb125-140">Debug</span></span>
  * <span data-ttu-id="cb125-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="cb125-141">EventSource</span></span>
  * <span data-ttu-id="cb125-142">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="cb125-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="cb125-143">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb125-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="cb125-144">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="cb125-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="cb125-145">Carrega a configuração do host de variáveis de ambiente prefixadas com "ASPNETCORE_".</span><span class="sxs-lookup"><span data-stu-id="cb125-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="cb125-146">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="cb125-147">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="cb125-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="cb125-148">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="cb125-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="cb125-149">Adiciona [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se ASPNETCORE_FORWARDEDHEADERS_ENABLED = true.</span><span class="sxs-lookup"><span data-stu-id="cb125-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="cb125-150">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="cb125-150">Enables IIS integration.</span></span> <span data-ttu-id="cb125-151">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="cb125-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="cb125-152">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="cb125-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="cb125-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="cb125-153">Framework-provided services</span></span>

<span data-ttu-id="cb125-154">Serviços que são registradosautomaticamente incluem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cb125-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="cb125-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cb125-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="cb125-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="cb125-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="cb125-157">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="cb125-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="cb125-158">Para obter mais informações sobre os serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="cb125-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="cb125-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cb125-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="cb125-160">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="cb125-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="cb125-161">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="cb125-162">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="cb125-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="cb125-163">O exemplo a seguir é uma implementação de `IHostedService` que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="cb125-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="cb125-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="cb125-164">IHostLifetime</span></span>

<span data-ttu-id="cb125-165">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="cb125-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="cb125-166">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="cb125-166">The last implementation registered is used.</span></span>

<span data-ttu-id="cb125-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="cb125-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="cb125-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="cb125-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="cb125-169">Escuta CTRL + C/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="cb125-169">Listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="cb125-170">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="cb125-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="cb125-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="cb125-171">IHostEnvironment</span></span>

<span data-ttu-id="cb125-172">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostEnvironment> em uma classe para obter informações sobre o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cb125-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="cb125-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="cb125-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="cb125-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="cb125-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="cb125-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="cb125-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="cb125-176">Os aplicativos Web implementam a interface `IWebHostEnvironment`, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="cb125-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="cb125-177">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="cb125-177">Host configuration</span></span>

<span data-ttu-id="cb125-178">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="cb125-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="cb125-179">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="cb125-180">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="cb125-181">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cb125-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="cb125-182">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="cb125-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="cb125-183">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="cb125-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="cb125-184">O provedor de variável de ambiente com o prefixo `DOTNET_` e os argumentos de linha de comando são incluídos pelo CreateDefaultBuilder.</span><span class="sxs-lookup"><span data-stu-id="cb125-184">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="cb125-185">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="cb125-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="cb125-186">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="cb125-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="cb125-187">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="cb125-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="cb125-188">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="cb125-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="cb125-189">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="cb125-189">App configuration</span></span>

<span data-ttu-id="cb125-190">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cb125-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="cb125-191">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="cb125-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="cb125-192">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="cb125-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="cb125-193">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="cb125-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="cb125-194">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="cb125-195">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="cb125-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="cb125-196">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="cb125-196">Settings for all app types</span></span>

<span data-ttu-id="cb125-197">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb125-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="cb125-198">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="cb125-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="cb125-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="cb125-199">ApplicationName</span></span>

<span data-ttu-id="cb125-200">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="cb125-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="cb125-201">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="cb125-201">**Key**: applicationName</span></span>  
<span data-ttu-id="cb125-202">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-202">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-203">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="cb125-204">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="cb125-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="cb125-205">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cb125-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="cb125-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="cb125-206">ContentRootPath</span></span>

<span data-ttu-id="cb125-207">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="cb125-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="cb125-208">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="cb125-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="cb125-209">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="cb125-209">**Key**: contentRoot</span></span>  
<span data-ttu-id="cb125-210">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-210">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-211">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="cb125-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="cb125-212">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="cb125-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="cb125-213">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="cb125-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="cb125-214">Para obter mais informações, consulte .</span><span class="sxs-lookup"><span data-stu-id="cb125-214">For more information, see:</span></span>

* [<span data-ttu-id="cb125-215">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="cb125-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="cb125-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="cb125-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="cb125-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="cb125-217">EnvironmentName</span></span>

<span data-ttu-id="cb125-218">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="cb125-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="cb125-219">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="cb125-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="cb125-220">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="cb125-220">Values aren't case sensitive.</span></span>

<span data-ttu-id="cb125-221">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="cb125-221">**Key**: environment</span></span>  
<span data-ttu-id="cb125-222">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-222">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-223">**Padrão**: Production</span><span class="sxs-lookup"><span data-stu-id="cb125-223">**Default**: Production</span></span>  
<span data-ttu-id="cb125-224">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="cb125-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="cb125-225">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="cb125-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="cb125-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="cb125-226">ShutdownTimeout</span></span>

<span data-ttu-id="cb125-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="cb125-228">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="cb125-228">The default value is five seconds.</span></span>  <span data-ttu-id="cb125-229">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="cb125-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="cb125-230">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="cb125-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="cb125-231">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="cb125-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="cb125-232">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="cb125-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="cb125-233">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="cb125-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="cb125-234">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="cb125-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="cb125-235">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="cb125-235">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="cb125-236">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="cb125-236">**Type**: *int*</span></span>  
<span data-ttu-id="cb125-237">**Padrão**: 5 segundos de **variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="cb125-237">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="cb125-238">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="cb125-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="cb125-239">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="cb125-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="cb125-240">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="cb125-240">Settings for web apps</span></span>

<span data-ttu-id="cb125-241">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb125-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="cb125-242">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="cb125-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="cb125-243">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="cb125-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="cb125-244">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb125-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="cb125-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="cb125-245">CaptureStartupErrors</span></span>

<span data-ttu-id="cb125-246">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="cb125-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="cb125-247">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="cb125-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="cb125-248">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="cb125-248">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="cb125-249">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="cb125-249">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="cb125-250">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="cb125-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="cb125-251">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="cb125-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="cb125-252">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="cb125-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="cb125-253">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="cb125-253">DetailedErrors</span></span>

<span data-ttu-id="cb125-254">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="cb125-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="cb125-255">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="cb125-255">**Key**: detailedErrors</span></span>  
<span data-ttu-id="cb125-256">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="cb125-256">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="cb125-257">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="cb125-257">**Default**: false</span></span>  
<span data-ttu-id="cb125-258">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="cb125-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="cb125-259">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="cb125-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="cb125-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="cb125-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="cb125-261">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="cb125-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="cb125-262">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="cb125-263">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="cb125-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="cb125-264">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="cb125-264">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="cb125-265">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-265">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-266">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="cb125-266">**Default**: Empty string</span></span>  
<span data-ttu-id="cb125-267">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="cb125-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="cb125-268">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="cb125-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="cb125-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="cb125-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="cb125-270">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="cb125-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="cb125-271">**Chave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="cb125-271">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="cb125-272">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-272">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-273">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="cb125-273">**Default**: Empty string</span></span>  
<span data-ttu-id="cb125-274">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="cb125-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="cb125-275">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="cb125-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="cb125-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="cb125-276">HTTPS_Port</span></span>

<span data-ttu-id="cb125-277">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cb125-277">The HTTPS redirect port.</span></span> <span data-ttu-id="cb125-278">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="cb125-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="cb125-279">**Chave**: https_port</span><span class="sxs-lookup"><span data-stu-id="cb125-279">**Key**: https_port</span></span>  
<span data-ttu-id="cb125-280">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-280">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-281">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="cb125-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="cb125-282">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="cb125-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="cb125-283">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="cb125-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="cb125-284">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="cb125-284">PreferHostingUrls</span></span>

<span data-ttu-id="cb125-285">Indica se o host deve escutar as URLs configuradas com o `IWebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="cb125-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="cb125-286">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="cb125-286">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="cb125-287">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="cb125-287">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="cb125-288">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="cb125-288">**Default**: true</span></span>  
<span data-ttu-id="cb125-289">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="cb125-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="cb125-290">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="cb125-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="cb125-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="cb125-291">PreventHostingStartup</span></span>

<span data-ttu-id="cb125-292">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="cb125-293">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cb125-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="cb125-294">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="cb125-294">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="cb125-295">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="cb125-295">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="cb125-296">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="cb125-296">**Default**: false</span></span>  
<span data-ttu-id="cb125-297">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="cb125-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="cb125-298">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="cb125-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="cb125-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="cb125-299">StartupAssembly</span></span>

<span data-ttu-id="cb125-300">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="cb125-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="cb125-301">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="cb125-301">**Key**: startupAssembly</span></span>  
<span data-ttu-id="cb125-302">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-302">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-303">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="cb125-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="cb125-304">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="cb125-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="cb125-305">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="cb125-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="cb125-306">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="cb125-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="cb125-307">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="cb125-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="cb125-308">URLs</span><span class="sxs-lookup"><span data-stu-id="cb125-308">URLs</span></span>

<span data-ttu-id="cb125-309">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="cb125-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="cb125-310">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="cb125-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="cb125-311">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="cb125-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="cb125-312">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="cb125-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="cb125-313">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="cb125-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="cb125-314">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="cb125-314">**Key**: urls</span></span>  
<span data-ttu-id="cb125-315">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-315">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-316">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="cb125-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="cb125-317">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="cb125-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="cb125-318">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="cb125-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="cb125-319">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="cb125-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="cb125-320">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cb125-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="cb125-321">WebRoot</span><span class="sxs-lookup"><span data-stu-id="cb125-321">WebRoot</span></span>

<span data-ttu-id="cb125-322">O caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-322">The relative path to the app's static assets.</span></span>

<span data-ttu-id="cb125-323">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="cb125-323">**Key**: webroot</span></span>  
<span data-ttu-id="cb125-324">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-324">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-325">**Padrão**: o padrão é `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="cb125-325">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="cb125-326">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="cb125-326">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="cb125-327">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="cb125-327">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="cb125-328">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="cb125-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="cb125-329">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="cb125-329">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="cb125-330">Para obter mais informações, consulte .</span><span class="sxs-lookup"><span data-stu-id="cb125-330">For more information, see:</span></span>

* [<span data-ttu-id="cb125-331">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="cb125-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="cb125-332">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="cb125-332">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="cb125-333">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="cb125-333">Manage the host lifetime</span></span>

<span data-ttu-id="cb125-334">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="cb125-335">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="cb125-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="cb125-336">Execute</span><span class="sxs-lookup"><span data-stu-id="cb125-336">Run</span></span>

<span data-ttu-id="cb125-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="cb125-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="cb125-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-338">RunAsync</span></span>

<span data-ttu-id="cb125-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="cb125-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="cb125-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-340">RunConsoleAsync</span></span>

<span data-ttu-id="cb125-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda Ctrl + C/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="cb125-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="cb125-342">Início</span><span class="sxs-lookup"><span data-stu-id="cb125-342">Start</span></span>

<span data-ttu-id="cb125-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="cb125-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="cb125-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-344">StartAsync</span></span>

<span data-ttu-id="cb125-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="cb125-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="cb125-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="cb125-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="cb125-347">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="cb125-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="cb125-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-348">StopAsync</span></span>

<span data-ttu-id="cb125-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="cb125-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="cb125-350">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="cb125-350">WaitForShutdown</span></span>

<span data-ttu-id="cb125-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloqueia o thread de chamada até que o desligamento seja disparado por IHostLifetime, por exemplo, por meio de Ctrl + C/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="cb125-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="cb125-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="cb125-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="cb125-354">Controle externo</span><span class="sxs-lookup"><span data-stu-id="cb125-354">External control</span></span>

<span data-ttu-id="cb125-355">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="cb125-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="cb125-356">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="cb125-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="cb125-357">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="cb125-358">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb125-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="cb125-359">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="cb125-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="cb125-360">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="cb125-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="cb125-361">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="cb125-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="cb125-362">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="cb125-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="cb125-363">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb125-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="cb125-364">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb125-364">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cb125-365">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="cb125-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="cb125-366">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="cb125-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="cb125-367">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="cb125-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="cb125-368">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="cb125-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="cb125-369">Na configuração **Inicialização do .NET Core (console)** , localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="cb125-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="cb125-370">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="cb125-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="cb125-371">Introdução</span><span class="sxs-lookup"><span data-stu-id="cb125-371">Introduction</span></span>

<span data-ttu-id="cb125-372">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="cb125-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="cb125-373">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="cb125-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="cb125-374"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="cb125-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="cb125-375">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="cb125-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="cb125-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="cb125-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="cb125-377">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="cb125-377">Set up a host</span></span>

<span data-ttu-id="cb125-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="cb125-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="cb125-379">Opções</span><span class="sxs-lookup"><span data-stu-id="cb125-379">Options</span></span>

<span data-ttu-id="cb125-380">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="cb125-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="cb125-381">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="cb125-381">Shutdown timeout</span></span>

<span data-ttu-id="cb125-382">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="cb125-383">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="cb125-383">The default value is five seconds.</span></span>

<span data-ttu-id="cb125-384">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="cb125-384">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="cb125-385">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="cb125-385">Default services</span></span>

<span data-ttu-id="cb125-386">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="cb125-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="cb125-387">[Ambiente](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="cb125-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="cb125-388">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="cb125-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="cb125-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="cb125-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="cb125-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="cb125-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="cb125-391">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="cb125-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="cb125-392">[Registro em log](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="cb125-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="cb125-393">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="cb125-393">Host configuration</span></span>

<span data-ttu-id="cb125-394">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="cb125-394">Host configuration is created by:</span></span>

* <span data-ttu-id="cb125-395">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="cb125-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="cb125-396">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="cb125-397">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="cb125-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="cb125-398">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="cb125-398">Application key (name)</span></span>

<span data-ttu-id="cb125-399">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="cb125-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="cb125-400">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="cb125-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="cb125-401">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="cb125-401">**Key**: applicationName</span></span>  
<span data-ttu-id="cb125-402">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-402">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-403">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="cb125-404">**Definido usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="cb125-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="cb125-405">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="cb125-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="cb125-406">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="cb125-406">Content root</span></span>

<span data-ttu-id="cb125-407">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="cb125-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="cb125-408">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="cb125-408">**Key**: contentRoot</span></span>  
<span data-ttu-id="cb125-409">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-409">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-410">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="cb125-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="cb125-411">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="cb125-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="cb125-412">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="cb125-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="cb125-413">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="cb125-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="cb125-414">Para obter mais informações, consulte [conceitos básicos: content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="cb125-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="cb125-415">Ambiente</span><span class="sxs-lookup"><span data-stu-id="cb125-415">Environment</span></span>

<span data-ttu-id="cb125-416">Define o [ambiente](xref:fundamentals/environments) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="cb125-417">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="cb125-417">**Key**: environment</span></span>  
<span data-ttu-id="cb125-418">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="cb125-418">**Type**: *string*</span></span>  
<span data-ttu-id="cb125-419">**Padrão**: Production</span><span class="sxs-lookup"><span data-stu-id="cb125-419">**Default**: Production</span></span>  
<span data-ttu-id="cb125-420">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="cb125-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="cb125-421">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="cb125-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="cb125-422">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="cb125-422">The environment can be set to any value.</span></span> <span data-ttu-id="cb125-423">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="cb125-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="cb125-424">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="cb125-424">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="cb125-425">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="cb125-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="cb125-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="cb125-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="cb125-427">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="cb125-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="cb125-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="cb125-429">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="cb125-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="cb125-430">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="cb125-430">No providers are included by default.</span></span> <span data-ttu-id="cb125-431">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="cb125-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="cb125-432">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="cb125-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="cb125-433">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cb125-433">Environment variable configuration.</span></span>
* <span data-ttu-id="cb125-434">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="cb125-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="cb125-435">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="cb125-435">Any other required configuration providers.</span></span>

<span data-ttu-id="cb125-436">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cb125-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="cb125-437">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cb125-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="cb125-438">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="cb125-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="cb125-439">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="cb125-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="cb125-440">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="cb125-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="cb125-441">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="cb125-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="cb125-442">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="cb125-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="cb125-443">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cb125-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="cb125-444">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb125-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="cb125-445">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cb125-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cb125-446">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="cb125-447">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="cb125-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="cb125-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cb125-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="cb125-449">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="cb125-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="cb125-450">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="cb125-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="cb125-451">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="cb125-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="cb125-452">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cb125-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="cb125-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="cb125-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="cb125-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="cb125-455">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="cb125-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="cb125-456">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="cb125-457">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="cb125-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="cb125-458">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="cb125-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="cb125-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cb125-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="cb125-460">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="cb125-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="cb125-461">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="cb125-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="cb125-462">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="cb125-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="cb125-463">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="cb125-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="cb125-464">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="cb125-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="cb125-465">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="cb125-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="cb125-466">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cb125-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="cb125-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="cb125-467">ConfigureServices</span></span>

<span data-ttu-id="cb125-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cb125-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="cb125-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="cb125-470">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="cb125-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="cb125-471">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="cb125-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="cb125-472">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cb125-472">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="cb125-473">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="cb125-473">ConfigureLogging</span></span>

<span data-ttu-id="cb125-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="cb125-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="cb125-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="cb125-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="cb125-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="cb125-476">UseConsoleLifetime</span></span>

<span data-ttu-id="cb125-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escuta Ctrl + C/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="cb125-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="cb125-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="cb125-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="cb125-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="cb125-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="cb125-480">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="cb125-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="cb125-481">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="cb125-481">Container configuration</span></span>

<span data-ttu-id="cb125-482">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="cb125-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="cb125-483">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="cb125-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="cb125-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="cb125-485">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="cb125-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="cb125-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="cb125-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="cb125-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="cb125-488">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cb125-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="cb125-489">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="cb125-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="cb125-490">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cb125-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="cb125-491">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="cb125-491">Extensibility</span></span>

<span data-ttu-id="cb125-492">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cb125-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="cb125-493">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="cb125-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="cb125-494">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="cb125-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="cb125-495">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="cb125-495">Manage the host</span></span>

<span data-ttu-id="cb125-496">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="cb125-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="cb125-497">Execute</span><span class="sxs-lookup"><span data-stu-id="cb125-497">Run</span></span>

<span data-ttu-id="cb125-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="cb125-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="cb125-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-499">RunAsync</span></span>

<span data-ttu-id="cb125-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="cb125-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="cb125-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-501">RunConsoleAsync</span></span>

<span data-ttu-id="cb125-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda Ctrl + C/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="cb125-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="cb125-503">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-503">Start and StopAsync</span></span>

<span data-ttu-id="cb125-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="cb125-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="cb125-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="cb125-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="cb125-506">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="cb125-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="cb125-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="cb125-509">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="cb125-509">WaitForShutdown</span></span>

<span data-ttu-id="cb125-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> é disparado por meio de <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escuta Ctrl + C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="cb125-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="cb125-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chamadas <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="cb125-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="cb125-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="cb125-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="cb125-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="cb125-514">Controle externo</span><span class="sxs-lookup"><span data-stu-id="cb125-514">External control</span></span>

<span data-ttu-id="cb125-515">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="cb125-515">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="cb125-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="cb125-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="cb125-517">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="cb125-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="cb125-518">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="cb125-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="cb125-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="cb125-520">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="cb125-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="cb125-521">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cb125-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="cb125-522">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cb125-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="cb125-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="cb125-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="cb125-524">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="cb125-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="cb125-525">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="cb125-525">Cancellation Token</span></span> | <span data-ttu-id="cb125-526">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="cb125-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="cb125-527">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="cb125-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="cb125-528">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="cb125-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="cb125-529">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="cb125-529">All requests should be processed.</span></span> <span data-ttu-id="cb125-530">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="cb125-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="cb125-531">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="cb125-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="cb125-532">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="cb125-532">Requests may still be processing.</span></span> <span data-ttu-id="cb125-533">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="cb125-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="cb125-534">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="cb125-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="cb125-535">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="cb125-535">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="cb125-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="cb125-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="cb125-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb125-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="cb125-538">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="cb125-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="cb125-539">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cb125-539">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
