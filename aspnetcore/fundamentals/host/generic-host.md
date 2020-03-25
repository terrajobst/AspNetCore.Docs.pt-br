---
title: Host Genérico .NET
author: rick-anderson
description: Saiba mais sobre o Host Genérico do .NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 0f8f03dabf65f2cbfe4c41d36b02a25d7902cefb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219214"
---
# <a name="net-generic-host"></a><span data-ttu-id="36ba7-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="36ba7-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="36ba7-104">Este artigo apresenta o Host Genérico do .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) e fornece diretrizes sobre como usá-lo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="36ba7-105">O que é um host?</span><span class="sxs-lookup"><span data-stu-id="36ba7-105">What's a host?</span></span>

<span data-ttu-id="36ba7-106">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="36ba7-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="36ba7-107">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="36ba7-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="36ba7-108">Registro em log</span><span class="sxs-lookup"><span data-stu-id="36ba7-108">Logging</span></span>
* <span data-ttu-id="36ba7-109">Configuração</span><span class="sxs-lookup"><span data-stu-id="36ba7-109">Configuration</span></span>
* <span data-ttu-id="36ba7-110">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="36ba7-110">`IHostedService` implementations</span></span>

<span data-ttu-id="36ba7-111">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="36ba7-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="36ba7-112">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="36ba7-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="36ba7-113">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36ba7-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="36ba7-114">Em versões do ASP.NET Core anteriores à 3.0, o [host da Web](xref:fundamentals/host/web-host) é usado para cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="36ba7-115">O host da Web não é mais recomendado para aplicativos Web e permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="36ba7-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="36ba7-116">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="36ba7-116">Set up a host</span></span>

<span data-ttu-id="36ba7-117">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="36ba7-118">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-118">The `Main` method:</span></span>

* <span data-ttu-id="36ba7-119">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="36ba7-120">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="36ba7-121">Eis aqui o código *Program.cs* para uma carga de trabalho não HTTP, com uma única implementação de `IHostedService` adicionada ao contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="36ba7-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="36ba7-122">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="36ba7-123">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="36ba7-124">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="36ba7-125">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="36ba7-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="36ba7-126">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="36ba7-126">Default builder settings</span></span>

<span data-ttu-id="36ba7-127">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="36ba7-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="36ba7-128">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="36ba7-129">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="36ba7-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="36ba7-130">Variáveis de ambiente prefixadas com `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="36ba7-131">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="36ba7-131">Command-line arguments.</span></span>
* <span data-ttu-id="36ba7-132">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="36ba7-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="36ba7-133">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36ba7-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="36ba7-134">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="36ba7-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="36ba7-135">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="36ba7-136">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-136">Environment variables.</span></span>
  * <span data-ttu-id="36ba7-137">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="36ba7-137">Command-line arguments.</span></span>
* <span data-ttu-id="36ba7-138">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="36ba7-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="36ba7-139">Console</span><span class="sxs-lookup"><span data-stu-id="36ba7-139">Console</span></span>
  * <span data-ttu-id="36ba7-140">Depuração</span><span class="sxs-lookup"><span data-stu-id="36ba7-140">Debug</span></span>
  * <span data-ttu-id="36ba7-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="36ba7-141">EventSource</span></span>
  * <span data-ttu-id="36ba7-142">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="36ba7-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="36ba7-143">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="36ba7-144">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="36ba7-145">Carrega a configuração do host de variáveis de ambiente prefixadas com `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="36ba7-146">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="36ba7-147">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="36ba7-148">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="36ba7-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="36ba7-149">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="36ba7-150">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="36ba7-150">Enables IIS integration.</span></span> <span data-ttu-id="36ba7-151">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="36ba7-152">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="36ba7-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="36ba7-153">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="36ba7-153">Framework-provided services</span></span>

<span data-ttu-id="36ba7-154">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="36ba7-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="36ba7-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="36ba7-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="36ba7-157">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="36ba7-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="36ba7-158">Para obter mais informações sobre os serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="36ba7-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="36ba7-160">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36ba7-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="36ba7-161">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="36ba7-162">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="36ba7-163">O exemplo a seguir é uma implementação de `IHostedService` que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="36ba7-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="36ba7-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-164">IHostLifetime</span></span>

<span data-ttu-id="36ba7-165">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="36ba7-166">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-166">The last implementation registered is used.</span></span>

<span data-ttu-id="36ba7-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="36ba7-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="36ba7-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="36ba7-169">Escuta <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="36ba7-170">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="36ba7-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="36ba7-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="36ba7-171">IHostEnvironment</span></span>

<span data-ttu-id="36ba7-172">Insira o serviço de <xref:Microsoft.Extensions.Hosting.IHostEnvironment> em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="36ba7-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="36ba7-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="36ba7-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="36ba7-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="36ba7-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="36ba7-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="36ba7-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="36ba7-176">Os aplicativos Web implementam a interface `IWebHostEnvironment`, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="36ba7-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="36ba7-177">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="36ba7-177">Host configuration</span></span>

<span data-ttu-id="36ba7-178">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="36ba7-179">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="36ba7-180">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="36ba7-181">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="36ba7-182">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="36ba7-183">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="36ba7-184">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="36ba7-185">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="36ba7-186">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="36ba7-187">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="36ba7-188">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="36ba7-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="36ba7-189">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="36ba7-189">App configuration</span></span>

<span data-ttu-id="36ba7-190">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="36ba7-191">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="36ba7-192">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="36ba7-193">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="36ba7-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="36ba7-194">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="36ba7-195">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="36ba7-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="36ba7-196">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="36ba7-196">Settings for all app types</span></span>

<span data-ttu-id="36ba7-197">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="36ba7-198">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="36ba7-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="36ba7-199">ApplicationName</span></span>

<span data-ttu-id="36ba7-200">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="36ba7-201">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="36ba7-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="36ba7-202">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-202">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-203">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="36ba7-204">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="36ba7-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="36ba7-205">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="36ba7-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="36ba7-206">ContentRootPath</span></span>

<span data-ttu-id="36ba7-207">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="36ba7-208">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="36ba7-209">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="36ba7-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="36ba7-210">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-210">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-211">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="36ba7-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="36ba7-212">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="36ba7-213">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="36ba7-214">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="36ba7-214">For more information, see:</span></span>

* [<span data-ttu-id="36ba7-215">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="36ba7-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="36ba7-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="36ba7-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="36ba7-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="36ba7-217">EnvironmentName</span></span>

<span data-ttu-id="36ba7-218">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="36ba7-219">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="36ba7-220">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="36ba7-221">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="36ba7-221">**Key**: `environment`</span></span>  
<span data-ttu-id="36ba7-222">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-222">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-223">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="36ba7-223">**Default**: `Production`</span></span>  
<span data-ttu-id="36ba7-224">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="36ba7-225">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="36ba7-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="36ba7-226">ShutdownTimeout</span></span>

<span data-ttu-id="36ba7-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="36ba7-228">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-228">The default value is five seconds.</span></span>  <span data-ttu-id="36ba7-229">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="36ba7-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="36ba7-230">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="36ba7-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="36ba7-231">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="36ba7-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="36ba7-232">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="36ba7-233">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="36ba7-234">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="36ba7-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="36ba7-235">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="36ba7-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="36ba7-236">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="36ba7-236">**Type**: `int`</span></span>  
<span data-ttu-id="36ba7-237">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="36ba7-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="36ba7-238">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="36ba7-239">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="36ba7-240">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="36ba7-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="36ba7-241">Desabilitar recarga de configuração de aplicativo na alteração</span><span class="sxs-lookup"><span data-stu-id="36ba7-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="36ba7-242">Por [padrão](xref:fundamentals/configuration/index#default), *appSettings. JSON* e *appSettings. { Ambiente}. JSON* são recarregados quando o arquivo é alterado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="36ba7-243">Para desabilitar esse comportamento de recarregamento no ASP.NET Core 5,0 Preview 3 ou posterior, defina a chave de `hostBuilder:reloadConfigOnChange` como `false`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="36ba7-244">**Chave**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="36ba7-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="36ba7-245">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-246">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="36ba7-246">**Default**: `true`</span></span>  
<span data-ttu-id="36ba7-247">**Argumento de linha de comando**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="36ba7-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="36ba7-248">**Variável de ambiente**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="36ba7-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="36ba7-249">O separador de dois pontos (`:`) não funciona com chaves hierárquicas de variável de ambiente em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="36ba7-250">Para obter mais informações, consulte [variáveis de ambiente](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="36ba7-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="36ba7-251">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="36ba7-251">Settings for web apps</span></span>

<span data-ttu-id="36ba7-252">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="36ba7-253">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="36ba7-254">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="36ba7-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="36ba7-255">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="36ba7-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="36ba7-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="36ba7-256">CaptureStartupErrors</span></span>

<span data-ttu-id="36ba7-257">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="36ba7-258">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="36ba7-259">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="36ba7-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="36ba7-260">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-261">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="36ba7-262">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="36ba7-263">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="36ba7-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="36ba7-264">DetailedErrors</span></span>

<span data-ttu-id="36ba7-265">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="36ba7-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="36ba7-266">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="36ba7-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="36ba7-267">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-268">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="36ba7-268">**Default**: `false`</span></span>  
<span data-ttu-id="36ba7-269">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="36ba7-270">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="36ba7-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="36ba7-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="36ba7-272">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="36ba7-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="36ba7-273">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="36ba7-274">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="36ba7-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="36ba7-275">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="36ba7-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="36ba7-276">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-276">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-277">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="36ba7-277">**Default**: Empty string</span></span>  
<span data-ttu-id="36ba7-278">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="36ba7-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="36ba7-279">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="36ba7-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="36ba7-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="36ba7-281">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="36ba7-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="36ba7-282">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="36ba7-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="36ba7-283">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-283">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-284">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="36ba7-284">**Default**: Empty string</span></span>  
<span data-ttu-id="36ba7-285">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="36ba7-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="36ba7-286">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="36ba7-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="36ba7-287">HTTPS_Port</span></span>

<span data-ttu-id="36ba7-288">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="36ba7-288">The HTTPS redirect port.</span></span> <span data-ttu-id="36ba7-289">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="36ba7-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="36ba7-290">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="36ba7-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="36ba7-291">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-291">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-292">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="36ba7-293">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="36ba7-294">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="36ba7-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="36ba7-295">PreferHostingUrls</span></span>

<span data-ttu-id="36ba7-296">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a implementação de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="36ba7-297">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="36ba7-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="36ba7-298">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-299">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="36ba7-299">**Default**: `true`</span></span>  
<span data-ttu-id="36ba7-300">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="36ba7-301">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="36ba7-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="36ba7-302">PreventHostingStartup</span></span>

<span data-ttu-id="36ba7-303">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="36ba7-304">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="36ba7-305">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="36ba7-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="36ba7-306">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-307">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="36ba7-307">**Default**: `false`</span></span>  
<span data-ttu-id="36ba7-308">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="36ba7-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="36ba7-309">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="36ba7-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="36ba7-310">StartupAssembly</span></span>

<span data-ttu-id="36ba7-311">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="36ba7-312">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="36ba7-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="36ba7-313">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-313">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-314">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="36ba7-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="36ba7-315">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="36ba7-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="36ba7-316">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="36ba7-317">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="36ba7-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="36ba7-318">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="36ba7-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="36ba7-319">URLs</span><span class="sxs-lookup"><span data-stu-id="36ba7-319">URLs</span></span>

<span data-ttu-id="36ba7-320">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="36ba7-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="36ba7-321">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="36ba7-322">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="36ba7-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="36ba7-323">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="36ba7-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="36ba7-324">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="36ba7-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="36ba7-325">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="36ba7-325">**Key**: `urls`</span></span>  
<span data-ttu-id="36ba7-326">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-326">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-327">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="36ba7-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="36ba7-328">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="36ba7-329">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="36ba7-330">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="36ba7-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="36ba7-331">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="36ba7-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="36ba7-332">WebRoot</span></span>

<span data-ttu-id="36ba7-333">O caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="36ba7-334">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="36ba7-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="36ba7-335">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-335">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-336">**Padrão**: o padrão é `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="36ba7-337">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="36ba7-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="36ba7-338">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="36ba7-339">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="36ba7-340">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="36ba7-341">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="36ba7-341">For more information, see:</span></span>

* [<span data-ttu-id="36ba7-342">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="36ba7-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="36ba7-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="36ba7-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="36ba7-344">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="36ba7-344">Manage the host lifetime</span></span>

<span data-ttu-id="36ba7-345">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="36ba7-346">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="36ba7-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="36ba7-347">Executar</span><span class="sxs-lookup"><span data-stu-id="36ba7-347">Run</span></span>

<span data-ttu-id="36ba7-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="36ba7-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-349">RunAsync</span></span>

<span data-ttu-id="36ba7-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="36ba7-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-351">RunConsoleAsync</span></span>

<span data-ttu-id="36ba7-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte a console, compila e inicia o host e aguarda o <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM ser desligado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="36ba7-353">Iniciar</span><span class="sxs-lookup"><span data-stu-id="36ba7-353">Start</span></span>

<span data-ttu-id="36ba7-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="36ba7-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="36ba7-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-355">StartAsync</span></span>

<span data-ttu-id="36ba7-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="36ba7-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="36ba7-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="36ba7-358">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="36ba7-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-359">StopAsync</span></span>

<span data-ttu-id="36ba7-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="36ba7-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="36ba7-361">WaitForShutdown</span></span>

<span data-ttu-id="36ba7-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="36ba7-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="36ba7-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="36ba7-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="36ba7-365">Controle externo</span><span class="sxs-lookup"><span data-stu-id="36ba7-365">External control</span></span>

<span data-ttu-id="36ba7-366">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="36ba7-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="36ba7-367">Este artigo apresenta o Host Genérico do .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) e fornece diretrizes sobre como usá-lo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="36ba7-368">O que é um host?</span><span class="sxs-lookup"><span data-stu-id="36ba7-368">What's a host?</span></span>

<span data-ttu-id="36ba7-369">Um *host* é um objeto que encapsula os recursos de um aplicativo, tais como:</span><span class="sxs-lookup"><span data-stu-id="36ba7-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="36ba7-370">DI (injeção de dependência)</span><span class="sxs-lookup"><span data-stu-id="36ba7-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="36ba7-371">Registro em log</span><span class="sxs-lookup"><span data-stu-id="36ba7-371">Logging</span></span>
* <span data-ttu-id="36ba7-372">Configuração</span><span class="sxs-lookup"><span data-stu-id="36ba7-372">Configuration</span></span>
* <span data-ttu-id="36ba7-373">Implementações de `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="36ba7-373">`IHostedService` implementations</span></span>

<span data-ttu-id="36ba7-374">Quando um host é iniciado, ele chama `IHostedService.StartAsync` em cada implementação de <xref:Microsoft.Extensions.Hosting.IHostedService> que encontra no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="36ba7-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="36ba7-375">Em um aplicativo Web, uma das implementações de `IHostedService` é um serviço Web que inicia uma [implementação do servidor HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="36ba7-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="36ba7-376">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36ba7-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="36ba7-377">Em versões do ASP.NET Core anteriores à 3.0, o [host da Web](xref:fundamentals/host/web-host) é usado para cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="36ba7-378">O host da Web não é mais recomendado para aplicativos Web e permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="36ba7-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="36ba7-379">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="36ba7-379">Set up a host</span></span>

<span data-ttu-id="36ba7-380">O host normalmente é configurado, compilado e executado pelo código na classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="36ba7-381">O método `Main`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-381">The `Main` method:</span></span>

* <span data-ttu-id="36ba7-382">Chama um método `CreateHostBuilder` para criar e configurar um objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="36ba7-383">Chama os métodos `Build` e `Run` no objeto construtor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="36ba7-384">Eis aqui o código *Program.cs* para uma carga de trabalho não HTTP, com uma única implementação de `IHostedService` adicionada ao contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="36ba7-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="36ba7-385">Para uma carga de trabalho HTTP, o método `Main` é o mesmo, mas `CreateHostBuilder` chama `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="36ba7-386">Se o aplicativo usar o Entity Framework Core, não altere o nome ou a assinatura do método `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="36ba7-387">As [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/) esperam encontrar um método `CreateHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="36ba7-388">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="36ba7-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="36ba7-389">Configurações do construtor padrão</span><span class="sxs-lookup"><span data-stu-id="36ba7-389">Default builder settings</span></span>

<span data-ttu-id="36ba7-390">O método <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="36ba7-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="36ba7-391">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="36ba7-392">Carrega a configuração do host de:</span><span class="sxs-lookup"><span data-stu-id="36ba7-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="36ba7-393">Variáveis de ambiente prefixadas com `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="36ba7-394">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="36ba7-394">Command-line arguments.</span></span>
* <span data-ttu-id="36ba7-395">Carrega a configuração do aplicativo de:</span><span class="sxs-lookup"><span data-stu-id="36ba7-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="36ba7-396">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36ba7-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="36ba7-397">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="36ba7-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="36ba7-398">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="36ba7-399">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-399">Environment variables.</span></span>
  * <span data-ttu-id="36ba7-400">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="36ba7-400">Command-line arguments.</span></span>
* <span data-ttu-id="36ba7-401">Adiciona os seguintes provedores de [registro em log](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="36ba7-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="36ba7-402">Console</span><span class="sxs-lookup"><span data-stu-id="36ba7-402">Console</span></span>
  * <span data-ttu-id="36ba7-403">Depuração</span><span class="sxs-lookup"><span data-stu-id="36ba7-403">Debug</span></span>
  * <span data-ttu-id="36ba7-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="36ba7-404">EventSource</span></span>
  * <span data-ttu-id="36ba7-405">EventLog (somente quando em execução no Windows)</span><span class="sxs-lookup"><span data-stu-id="36ba7-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="36ba7-406">Habilita a [validação de escopo](xref:fundamentals/dependency-injection#scope-validation) e a [validação de dependência](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) quando o ambiente é o de Desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="36ba7-407">O método `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="36ba7-408">Carrega a configuração do host de variáveis de ambiente prefixadas com `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="36ba7-409">Define o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web e configura-o usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="36ba7-410">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="36ba7-411">Adiciona [middleware de filtragem de Host](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="36ba7-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="36ba7-412">Adiciona o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) se `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for igual a `true`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="36ba7-413">Habilita a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="36ba7-413">Enables IIS integration.</span></span> <span data-ttu-id="36ba7-414">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="36ba7-415">As seções [Configurações para todos os tipos de aplicativo](#settings-for-all-app-types) e [Configurações para aplicativos Web](#settings-for-web-apps) neste artigo mostram como substituir as configurações do construtor padrão.</span><span class="sxs-lookup"><span data-stu-id="36ba7-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="36ba7-416">Serviços fornecidos pela estrutura</span><span class="sxs-lookup"><span data-stu-id="36ba7-416">Framework-provided services</span></span>

<span data-ttu-id="36ba7-417">Os seguintes serviços são registrados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="36ba7-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="36ba7-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="36ba7-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="36ba7-420">IHostEnvironment/IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="36ba7-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="36ba7-421">Para obter mais informações sobre os serviços fornecidos pela estrutura, consulte <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="36ba7-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="36ba7-423">Injete o serviço <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (anteriormente conhecido como `IApplicationLifetime`) em qualquer classe para lidar com tarefas de pós-inicialização e de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36ba7-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="36ba7-424">Três propriedades na interface são tokens de cancelamento usados para registrar métodos de manipulador de eventos de inicialização e desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="36ba7-425">A interface também inclui um método `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="36ba7-426">O exemplo a seguir é uma implementação de `IHostedService` que registra `IHostApplicationLifetime` eventos:</span><span class="sxs-lookup"><span data-stu-id="36ba7-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="36ba7-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-427">IHostLifetime</span></span>

<span data-ttu-id="36ba7-428">A implementação <xref:Microsoft.Extensions.Hosting.IHostLifetime> controla quando o host é iniciado e quando ele é interrompido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="36ba7-429">A última implementação registrada é usada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-429">The last implementation registered is used.</span></span>

<span data-ttu-id="36ba7-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é a implementação `IHostLifetime` padrão.</span><span class="sxs-lookup"><span data-stu-id="36ba7-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="36ba7-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="36ba7-432">Escuta <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="36ba7-433">Desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="36ba7-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="36ba7-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="36ba7-434">IHostEnvironment</span></span>

<span data-ttu-id="36ba7-435">Insira o serviço de <xref:Microsoft.Extensions.Hosting.IHostEnvironment> em uma classe para obter informações sobre as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="36ba7-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="36ba7-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="36ba7-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="36ba7-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="36ba7-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="36ba7-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="36ba7-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="36ba7-439">Os aplicativos Web implementam a interface `IWebHostEnvironment`, que herda `IHostEnvironment` e adiciona o [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="36ba7-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="36ba7-440">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="36ba7-440">Host configuration</span></span>

<span data-ttu-id="36ba7-441">A configuração do host é usada para as propriedades da implementação <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="36ba7-442">A configuração do host está disponível por meio de [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) dentro de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="36ba7-443">Após `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` é substituído com a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="36ba7-444">Para adicionar a configuração do host, chame <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="36ba7-445">`ConfigureHostConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="36ba7-446">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="36ba7-447">O provedor de variáveis de ambiente com prefixo `DOTNET_` e argumentos de linha de comando são incluídos por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="36ba7-448">Para aplicativos Web, o provedor de variáveis de ambiente com o prefixo `ASPNETCORE_` é adicionado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="36ba7-449">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="36ba7-450">Por exemplo, o valor da variável de ambiente de `ASPNETCORE_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="36ba7-451">O exemplo a seguir cria a configuração de host:</span><span class="sxs-lookup"><span data-stu-id="36ba7-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="36ba7-452">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="36ba7-452">App configuration</span></span>

<span data-ttu-id="36ba7-453">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> em `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="36ba7-454">`ConfigureAppConfiguration` pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="36ba7-455">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="36ba7-456">A configuração criada pelo `ConfigureAppConfiguration` está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e como um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="36ba7-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="36ba7-457">A configuração do host também é adicionada à configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="36ba7-458">Para obter mais informações, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="36ba7-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="36ba7-459">Configurações para todos os tipos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="36ba7-459">Settings for all app types</span></span>

<span data-ttu-id="36ba7-460">Esta seção lista as configurações de host que se aplicam a cargas de trabalho HTTP e àquelas não HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="36ba7-461">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="36ba7-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="36ba7-462">ApplicationName</span></span>

<span data-ttu-id="36ba7-463">A propriedade [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="36ba7-464">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="36ba7-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="36ba7-465">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-465">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-466">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="36ba7-467">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="36ba7-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="36ba7-468">Para definir esse valor, use a variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="36ba7-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="36ba7-469">ContentRootPath</span></span>

<span data-ttu-id="36ba7-470">A propriedade [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) determina o local em que o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="36ba7-471">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="36ba7-472">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="36ba7-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="36ba7-473">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-473">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-474">**Padrão**: a pasta onde o assembly de aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="36ba7-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="36ba7-475">**Variável de ambiente**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="36ba7-476">Para definir esse valor, use a variável de ambiente ou a chamada `UseContentRoot` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="36ba7-477">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="36ba7-477">For more information, see:</span></span>

* [<span data-ttu-id="36ba7-478">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="36ba7-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="36ba7-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="36ba7-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="36ba7-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="36ba7-480">EnvironmentName</span></span>

<span data-ttu-id="36ba7-481">A propriedade [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) pode ser definida para qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="36ba7-482">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="36ba7-483">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="36ba7-484">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="36ba7-484">**Key**: `environment`</span></span>  
<span data-ttu-id="36ba7-485">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-485">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-486">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="36ba7-486">**Default**: `Production`</span></span>  
<span data-ttu-id="36ba7-487">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="36ba7-488">Para definir esse valor, use a variável de ambiente ou a chamada `UseEnvironment` em `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="36ba7-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="36ba7-489">ShutdownTimeout</span></span>

<span data-ttu-id="36ba7-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="36ba7-491">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-491">The default value is five seconds.</span></span>  <span data-ttu-id="36ba7-492">Durante o período de tempo limite, o host:</span><span class="sxs-lookup"><span data-stu-id="36ba7-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="36ba7-493">Dispara [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="36ba7-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="36ba7-494">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="36ba7-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="36ba7-495">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="36ba7-496">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="36ba7-497">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="36ba7-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="36ba7-498">**Chave**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="36ba7-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="36ba7-499">**Tipo**: `int`</span><span class="sxs-lookup"><span data-stu-id="36ba7-499">**Type**: `int`</span></span>  
<span data-ttu-id="36ba7-500">**Padrão**: 5 segundos</span><span class="sxs-lookup"><span data-stu-id="36ba7-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="36ba7-501">**Variável de ambiente**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="36ba7-502">Para definir esse valor, use a variável de ambiente ou configure `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="36ba7-503">O exemplo a seguir define o tempo limite para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="36ba7-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="36ba7-504">Configurações para aplicativos Web</span><span class="sxs-lookup"><span data-stu-id="36ba7-504">Settings for web apps</span></span>

<span data-ttu-id="36ba7-505">Algumas configurações de host se aplicam somente a cargas de trabalho HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="36ba7-506">Por padrão, variáveis de ambiente usadas para definir essas configurações podem ter um prefixo `DOTNET_` ou `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="36ba7-507">Métodos de extensão em `IWebHostBuilder` estão disponíveis para essas configurações.</span><span class="sxs-lookup"><span data-stu-id="36ba7-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="36ba7-508">Exemplos de código que mostram como chamar os métodos de extensão pressupõem que `webBuilder` é uma instância de `IWebHostBuilder`, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="36ba7-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="36ba7-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="36ba7-509">CaptureStartupErrors</span></span>

<span data-ttu-id="36ba7-510">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="36ba7-511">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="36ba7-512">**Chave**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="36ba7-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="36ba7-513">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-514">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="36ba7-515">**Variável de ambiente**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="36ba7-516">Para definir esse valor, use a configuração ou a chamada `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="36ba7-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="36ba7-517">DetailedErrors</span></span>

<span data-ttu-id="36ba7-518">Quando habilitado (ou quando o ambiente é `Development`), o aplicativo captura erros detalhados.</span><span class="sxs-lookup"><span data-stu-id="36ba7-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="36ba7-519">**Chave**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="36ba7-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="36ba7-520">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-521">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="36ba7-521">**Default**: `false`</span></span>  
<span data-ttu-id="36ba7-522">**Variável de ambiente**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="36ba7-523">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="36ba7-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="36ba7-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="36ba7-525">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="36ba7-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="36ba7-526">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="36ba7-527">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="36ba7-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="36ba7-528">**Chave**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="36ba7-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="36ba7-529">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-529">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-530">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="36ba7-530">**Default**: Empty string</span></span>  
<span data-ttu-id="36ba7-531">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="36ba7-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="36ba7-532">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="36ba7-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="36ba7-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="36ba7-534">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="36ba7-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="36ba7-535">**Chave**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="36ba7-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="36ba7-536">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-536">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-537">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="36ba7-537">**Default**: Empty string</span></span>  
<span data-ttu-id="36ba7-538">**Variável de ambiente**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="36ba7-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="36ba7-539">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="36ba7-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="36ba7-540">HTTPS_Port</span></span>

<span data-ttu-id="36ba7-541">A porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="36ba7-541">The HTTPS redirect port.</span></span> <span data-ttu-id="36ba7-542">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="36ba7-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="36ba7-543">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="36ba7-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="36ba7-544">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-544">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-545">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="36ba7-546">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="36ba7-547">Para definir esse valor, use a configuração ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="36ba7-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="36ba7-548">PreferHostingUrls</span></span>

<span data-ttu-id="36ba7-549">Indica se o host deve escutar nas URLs configuradas com o `IWebHostBuilder` em vez das URLs configuradas com a implementação de `IServer`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="36ba7-550">**Chave**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="36ba7-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="36ba7-551">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-552">**Padrão**: `true`</span><span class="sxs-lookup"><span data-stu-id="36ba7-552">**Default**: `true`</span></span>  
<span data-ttu-id="36ba7-553">**Variável de ambiente**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="36ba7-554">Para definir esse valor, use a variável de ambiente ou a chamada `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="36ba7-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="36ba7-555">PreventHostingStartup</span></span>

<span data-ttu-id="36ba7-556">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="36ba7-557">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="36ba7-558">**Chave**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="36ba7-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="36ba7-559">**Tipo**: `bool` (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="36ba7-560">**Padrão**: `false`</span><span class="sxs-lookup"><span data-stu-id="36ba7-560">**Default**: `false`</span></span>  
<span data-ttu-id="36ba7-561">**Variável de ambiente**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="36ba7-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="36ba7-562">Para definir esse valor, use a variável de ambiente ou a chamada `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="36ba7-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="36ba7-563">StartupAssembly</span></span>

<span data-ttu-id="36ba7-564">O assembly no qual pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="36ba7-565">**Chave**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="36ba7-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="36ba7-566">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-566">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-567">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="36ba7-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="36ba7-568">**Variável de ambiente**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="36ba7-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="36ba7-569">Para definir esse valor, use a variável de ambiente ou a chamada `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="36ba7-570">`UseStartup` pode usar um nome de assembly (`string`) ou um tipo (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="36ba7-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="36ba7-571">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="36ba7-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="36ba7-572">URLs</span><span class="sxs-lookup"><span data-stu-id="36ba7-572">URLs</span></span>

<span data-ttu-id="36ba7-573">Uma lista delimitada por ponto-e-vírgula de endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="36ba7-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="36ba7-574">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="36ba7-575">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="36ba7-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="36ba7-576">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="36ba7-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="36ba7-577">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="36ba7-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="36ba7-578">**Chave**: `urls`</span><span class="sxs-lookup"><span data-stu-id="36ba7-578">**Key**: `urls`</span></span>  
<span data-ttu-id="36ba7-579">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-579">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-580">**Padrão**: `http://localhost:5000` e `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="36ba7-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="36ba7-581">**Variável de ambiente**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="36ba7-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="36ba7-582">Para definir esse valor, use a variável de ambiente ou a chamada `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="36ba7-583">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="36ba7-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="36ba7-584">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="36ba7-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="36ba7-585">WebRoot</span></span>

<span data-ttu-id="36ba7-586">O caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="36ba7-587">**Chave**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="36ba7-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="36ba7-588">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-588">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-589">**Padrão**: o padrão é `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="36ba7-590">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="36ba7-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="36ba7-591">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="36ba7-592">**Variável de ambiente**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="36ba7-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="36ba7-593">Para definir esse valor, use a variável de ambiente ou a chamada `UseWebRoot`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="36ba7-594">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="36ba7-594">For more information, see:</span></span>

* [<span data-ttu-id="36ba7-595">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="36ba7-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="36ba7-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="36ba7-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="36ba7-597">Gerenciar o tempo de vida do host</span><span class="sxs-lookup"><span data-stu-id="36ba7-597">Manage the host lifetime</span></span>

<span data-ttu-id="36ba7-598">Chame métodos na implementação de <xref:Microsoft.Extensions.Hosting.IHost> criada para iniciar e parar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="36ba7-599">Esses métodos afetam todas as implementações de <xref:Microsoft.Extensions.Hosting.IHostedService> que são registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="36ba7-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="36ba7-600">Executar</span><span class="sxs-lookup"><span data-stu-id="36ba7-600">Run</span></span>

<span data-ttu-id="36ba7-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="36ba7-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-602">RunAsync</span></span>

<span data-ttu-id="36ba7-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="36ba7-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-604">RunConsoleAsync</span></span>

<span data-ttu-id="36ba7-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte a console, compila e inicia o host e aguarda o <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM ser desligado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="36ba7-606">Iniciar</span><span class="sxs-lookup"><span data-stu-id="36ba7-606">Start</span></span>

<span data-ttu-id="36ba7-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="36ba7-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="36ba7-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-608">StartAsync</span></span>

<span data-ttu-id="36ba7-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o host e retorna um <xref:System.Threading.Tasks.Task>, que é concluído quando o token de cancelamento ou o desligamento é disparado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="36ba7-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de `StartAsync`, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="36ba7-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="36ba7-611">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="36ba7-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-612">StopAsync</span></span>

<span data-ttu-id="36ba7-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="36ba7-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="36ba7-614">WaitForShutdown</span></span>

<span data-ttu-id="36ba7-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> bloqueia o thread de chamada até que o desligamento seja disparado pelo IHostLifetime, como por meio de <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="36ba7-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="36ba7-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="36ba7-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="36ba7-618">Controle externo</span><span class="sxs-lookup"><span data-stu-id="36ba7-618">External control</span></span>

<span data-ttu-id="36ba7-619">O controle direto do tempo de vida do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="36ba7-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="36ba7-620">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="36ba7-621">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="36ba7-622">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="36ba7-623">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="36ba7-624">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="36ba7-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="36ba7-625">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="36ba7-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="36ba7-626">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="36ba7-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="36ba7-627">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="36ba7-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="36ba7-628">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36ba7-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="36ba7-629">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="36ba7-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="36ba7-630">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="36ba7-631">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="36ba7-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="36ba7-632">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="36ba7-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="36ba7-633">Na configuração **Inicialização do .NET Core (console)** , localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="36ba7-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="36ba7-634">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="36ba7-635">Introdução</span><span class="sxs-lookup"><span data-stu-id="36ba7-635">Introduction</span></span>

<span data-ttu-id="36ba7-636">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="36ba7-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="36ba7-637">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="36ba7-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="36ba7-638"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="36ba7-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="36ba7-639">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="36ba7-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="36ba7-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="36ba7-641">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="36ba7-641">Set up a host</span></span>

<span data-ttu-id="36ba7-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="36ba7-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="36ba7-643">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="36ba7-643">Options</span></span>

<span data-ttu-id="36ba7-644">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="36ba7-645">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="36ba7-645">Shutdown timeout</span></span>

<span data-ttu-id="36ba7-646">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="36ba7-647">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-647">The default value is five seconds.</span></span>

<span data-ttu-id="36ba7-648">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="36ba7-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="36ba7-649">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="36ba7-649">Default services</span></span>

<span data-ttu-id="36ba7-650">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="36ba7-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="36ba7-651">[Ambiente](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="36ba7-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="36ba7-652">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="36ba7-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="36ba7-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="36ba7-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="36ba7-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="36ba7-655">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="36ba7-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="36ba7-656">[Registro em log](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="36ba7-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="36ba7-657">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="36ba7-657">Host configuration</span></span>

<span data-ttu-id="36ba7-658">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="36ba7-658">Host configuration is created by:</span></span>

* <span data-ttu-id="36ba7-659">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="36ba7-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="36ba7-660">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="36ba7-661">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="36ba7-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="36ba7-662">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="36ba7-662">Application key (name)</span></span>

<span data-ttu-id="36ba7-663">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="36ba7-664">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="36ba7-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="36ba7-665">**Chave**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="36ba7-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="36ba7-666">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-666">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-667">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="36ba7-668">**Definido usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="36ba7-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="36ba7-669">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="36ba7-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="36ba7-670">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="36ba7-670">Content root</span></span>

<span data-ttu-id="36ba7-671">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="36ba7-672">**Chave**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="36ba7-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="36ba7-673">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-673">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-674">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="36ba7-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="36ba7-675">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="36ba7-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="36ba7-676">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="36ba7-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="36ba7-677">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="36ba7-678">Para obter mais informações, consulte [conceitos básicos: content root](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="36ba7-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="36ba7-679">Ambiente</span><span class="sxs-lookup"><span data-stu-id="36ba7-679">Environment</span></span>

<span data-ttu-id="36ba7-680">Define o [ambiente](xref:fundamentals/environments) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="36ba7-681">**Chave**: `environment`</span><span class="sxs-lookup"><span data-stu-id="36ba7-681">**Key**: `environment`</span></span>  
<span data-ttu-id="36ba7-682">**Tipo**: `string`</span><span class="sxs-lookup"><span data-stu-id="36ba7-682">**Type**: `string`</span></span>  
<span data-ttu-id="36ba7-683">**Padrão**: `Production`</span><span class="sxs-lookup"><span data-stu-id="36ba7-683">**Default**: `Production`</span></span>  
<span data-ttu-id="36ba7-684">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="36ba7-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="36ba7-685">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="36ba7-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="36ba7-686">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="36ba7-686">The environment can be set to any value.</span></span> <span data-ttu-id="36ba7-687">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="36ba7-688">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="36ba7-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="36ba7-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="36ba7-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="36ba7-691">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="36ba7-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="36ba7-693">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="36ba7-694">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="36ba7-694">No providers are included by default.</span></span> <span data-ttu-id="36ba7-695">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="36ba7-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="36ba7-696">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="36ba7-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="36ba7-697">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-697">Environment variable configuration.</span></span>
* <span data-ttu-id="36ba7-698">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="36ba7-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="36ba7-699">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="36ba7-699">Any other required configuration providers.</span></span>

<span data-ttu-id="36ba7-700">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="36ba7-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="36ba7-701">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="36ba7-702">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="36ba7-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="36ba7-703">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="36ba7-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="36ba7-704">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="36ba7-705">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="36ba7-706">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="36ba7-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="36ba7-707">Durante o desenvolvimento, ao usar o [Visual Studio](https://visualstudio.microsoft.com) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36ba7-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="36ba7-708">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="36ba7-709">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="36ba7-710">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="36ba7-711">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="36ba7-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="36ba7-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="36ba7-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="36ba7-713">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="36ba7-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="36ba7-714">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="36ba7-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="36ba7-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="36ba7-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="36ba7-716">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="36ba7-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="36ba7-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="36ba7-719">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="36ba7-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="36ba7-720">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="36ba7-721">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="36ba7-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="36ba7-722">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="36ba7-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="36ba7-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="36ba7-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="36ba7-724">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="36ba7-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="36ba7-725">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="36ba7-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="36ba7-726">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="36ba7-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="36ba7-727">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="36ba7-728">Métodos de extensão de configuração, como <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> e <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, exigem pacotes adicionais do NuGet, como [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) e [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="36ba7-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="36ba7-729">A menos que o aplicativo use o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), esses pacotes devem ser adicionados ao projeto, além do pacote principal [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="36ba7-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="36ba7-730">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="36ba7-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="36ba7-731">ConfigureServices</span></span>

<span data-ttu-id="36ba7-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="36ba7-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="36ba7-734">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="36ba7-735">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="36ba7-736">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="36ba7-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="36ba7-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="36ba7-737">ConfigureLogging</span></span>

<span data-ttu-id="36ba7-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="36ba7-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="36ba7-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-740">UseConsoleLifetime</span></span>

<span data-ttu-id="36ba7-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escuta <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="36ba7-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="36ba7-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="36ba7-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="36ba7-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="36ba7-744">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="36ba7-745">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="36ba7-745">Container configuration</span></span>

<span data-ttu-id="36ba7-746">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="36ba7-747">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="36ba7-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="36ba7-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="36ba7-749">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="36ba7-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="36ba7-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="36ba7-752">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="36ba7-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="36ba7-753">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="36ba7-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="36ba7-754">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="36ba7-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="36ba7-755">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="36ba7-755">Extensibility</span></span>

<span data-ttu-id="36ba7-756">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="36ba7-757">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="36ba7-758">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="36ba7-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="36ba7-759">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="36ba7-759">Manage the host</span></span>

<span data-ttu-id="36ba7-760">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="36ba7-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="36ba7-761">Executar</span><span class="sxs-lookup"><span data-stu-id="36ba7-761">Run</span></span>

<span data-ttu-id="36ba7-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="36ba7-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="36ba7-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-763">RunAsync</span></span>

<span data-ttu-id="36ba7-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="36ba7-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="36ba7-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-765">RunConsoleAsync</span></span>

<span data-ttu-id="36ba7-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte a console, compila e inicia o host e aguarda o <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM ser desligado.</span><span class="sxs-lookup"><span data-stu-id="36ba7-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="36ba7-767">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-767">Start and StopAsync</span></span>

<span data-ttu-id="36ba7-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="36ba7-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="36ba7-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="36ba7-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="36ba7-770">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="36ba7-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="36ba7-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="36ba7-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="36ba7-773">WaitForShutdown</span></span>

<span data-ttu-id="36ba7-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> é disparado por meio do <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (escuta <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="36ba7-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="36ba7-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="36ba7-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="36ba7-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="36ba7-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="36ba7-778">Controle externo</span><span class="sxs-lookup"><span data-stu-id="36ba7-778">External control</span></span>

<span data-ttu-id="36ba7-779">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="36ba7-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="36ba7-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="36ba7-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="36ba7-781">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="36ba7-782">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="36ba7-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="36ba7-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="36ba7-784">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="36ba7-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="36ba7-785">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="36ba7-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="36ba7-786">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="36ba7-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="36ba7-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36ba7-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="36ba7-788">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="36ba7-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="36ba7-789">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="36ba7-789">Cancellation Token</span></span> | <span data-ttu-id="36ba7-790">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="36ba7-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="36ba7-791">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="36ba7-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="36ba7-792">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36ba7-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="36ba7-793">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-793">All requests should be processed.</span></span> <span data-ttu-id="36ba7-794">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="36ba7-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="36ba7-795">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="36ba7-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="36ba7-796">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="36ba7-796">Requests may still be processing.</span></span> <span data-ttu-id="36ba7-797">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="36ba7-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="36ba7-798">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="36ba7-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="36ba7-799">O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="36ba7-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="36ba7-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="36ba7-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="36ba7-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36ba7-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="36ba7-802">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="36ba7-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="36ba7-803">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="36ba7-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
