---
title: Host da Web do ASP.NET Core
author: guardrex
description: Saiba mais sobre o host da Web no ASP.NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: d387098662cc832cc0e49b6a1636f0ebcc7308de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081683"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="a99d9-103">Host da Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a99d9-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="a99d9-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a99d9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a99d9-105">Aplicativos ASP.NET Core configuram e inicializam um *host*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-105">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="a99d9-106">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-106">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a99d9-107">No mínimo, o host configura um servidor e um pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="a99d9-107">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="a99d9-108">O host também pode configurar registro em log, a injeção de dependência e a configuração.</span><span class="sxs-lookup"><span data-stu-id="a99d9-108">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a99d9-109">Este artigo aborda o host da Web, que permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="a99d9-109">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="a99d9-110">O [Host genérico](xref:fundamentals/host/generic-host) é recomendado para todos os tipos de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-110">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a99d9-111">Este artigo aborda o host da Web, que serve para hospedar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="a99d9-111">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="a99d9-112">Para outros tipos de aplicativos, use o [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a99d9-112">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="a99d9-113">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="a99d9-113">Set up a host</span></span>

<span data-ttu-id="a99d9-114">Crie um host usando uma instância do [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="a99d9-114">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="a99d9-115">Normalmente, isso é feito no ponto de entrada do aplicativo, o método `Main`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-115">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="a99d9-116">Em modelos de projeto, `Main` está localizado em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-116">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="a99d9-117">Um aplicativo típico chama [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para começar a configurar um host:</span><span class="sxs-lookup"><span data-stu-id="a99d9-117">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="a99d9-118">O código que chama `CreateDefaultBuilder` está em um método chamado `CreateWebHostBuilder`, que o separa do código em `Main`, que chama `Run` no objeto de construtor.</span><span class="sxs-lookup"><span data-stu-id="a99d9-118">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="a99d9-119">Essa separação será necessária se você usar [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="a99d9-119">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="a99d9-120">As ferramentas esperam encontrar um método `CreateWebHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-120">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="a99d9-121">Uma alternativa é implementar `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-121">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="a99d9-122">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="a99d9-122">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="a99d9-123">`CreateDefaultBuilder` executa as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="a99d9-123">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="a99d9-124">Configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-124">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="a99d9-125">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-125">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="a99d9-126">Define a raiz do conteúdo como o caminho retornado por [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="a99d9-126">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="a99d9-127">Carrega a [configuração do host](#host-configuration-values) de:</span><span class="sxs-lookup"><span data-stu-id="a99d9-127">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="a99d9-128">Variáveis de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="a99d9-128">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="a99d9-129">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="a99d9-129">Command-line arguments.</span></span>
* <span data-ttu-id="a99d9-130">Carrega a configuração do aplicativo na seguinte ordem de:</span><span class="sxs-lookup"><span data-stu-id="a99d9-130">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="a99d9-131">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-131">*appsettings.json*.</span></span>
  * <span data-ttu-id="a99d9-132">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-132">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="a99d9-133">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="a99d9-133">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a99d9-134">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="a99d9-134">Environment variables.</span></span>
  * <span data-ttu-id="a99d9-135">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="a99d9-135">Command-line arguments.</span></span>
* <span data-ttu-id="a99d9-136">Configura o [registro em log](xref:fundamentals/logging/index) para a saída do console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="a99d9-136">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="a99d9-137">O registro em log inclui regras de [filtragem de log](xref:fundamentals/logging/index#log-filtering) especificadas em uma seção de configuração de registro em log de um arquivo *appsettings.json* ou *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-137">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="a99d9-138">Quando executado com a proteção do IIS com o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` habilita a [Integração do IIS](xref:host-and-deploy/iis/index), que configura a porta e o endereço básico do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-138">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="a99d9-139">A Integração do IIS também configura o aplicativo para [capturar erros de inicialização](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="a99d9-139">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="a99d9-140">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-140">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="a99d9-141">Definirá [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` se o ambiente do aplicativo for de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a99d9-141">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="a99d9-142">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="a99d9-142">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="a99d9-143">A configuração definida por `CreateDefaultBuilder` pode ser substituída e aumentada por [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) e outros métodos, bem como os métodos de extensão de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="a99d9-143">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="a99d9-144">Veja a seguir alguns exemplos:</span><span class="sxs-lookup"><span data-stu-id="a99d9-144">A few examples follow:</span></span>

* <span data-ttu-id="a99d9-145">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) é usado para especificar `IConfiguration` adicionais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-145">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="a99d9-146">A seguinte chamada de `ConfigureAppConfiguration` adiciona um delegado para incluir a configuração do aplicativo no arquivo *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-146">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="a99d9-147">`ConfigureAppConfiguration` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="a99d9-147">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="a99d9-148">Observe que essa configuração não se aplica ao host (por exemplo, URLs de servidor ou de ambiente).</span><span class="sxs-lookup"><span data-stu-id="a99d9-148">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="a99d9-149">Consulte a seção [Valores de configuração de Host](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="a99d9-149">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="a99d9-150">A seguinte chamada de `ConfigureLogging` adiciona um delegado para configurar o nível de log mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) como [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="a99d9-150">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="a99d9-151">Essa configuração substitui as configurações em *appsettings.Development.json* (`LogLevel.Debug`) e *appsettings.Production.json* (`LogLevel.Error`) configuradas por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-151">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a99d9-152">`ConfigureLogging` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="a99d9-152">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="a99d9-153">A seguinte chamada para `ConfigureKestrel` substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 milhões de bytes, estabelecido quando o Kestrel foi configurado pelo `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-153">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="a99d9-154">A seguinte chamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30.000.000 bytes estabelecido quando Kestrel foi configurado por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-154">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="a99d9-155">A *raiz do conteúdo* determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="a99d9-155">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="a99d9-156">Quando o aplicativo é iniciado na pasta raiz do projeto, essa pasta é usada como a raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-156">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="a99d9-157">Esse é o padrão usado no [Visual Studio](https://visualstudio.microsoft.com) e nos [novos modelos dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="a99d9-157">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="a99d9-158">Para obter mais informações sobre a configuração de aplicativo, veja <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-158">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="a99d9-159">Como uma alternativa ao uso do método `CreateDefaultBuilder` estático, criar um host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) é uma abordagem compatível com o ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="a99d9-159">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="a99d9-160">Ao configurar um host, os métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) podem ser fornecidos.</span><span class="sxs-lookup"><span data-stu-id="a99d9-160">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="a99d9-161">Se uma classe `Startup` for especificada, ela deverá definir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-161">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="a99d9-162">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-162">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="a99d9-163">Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="a99d9-163">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="a99d9-164">Diversas chamadas para `Configure` ou `UseStartup` no `WebHostBuilder` substituem configurações anteriores.</span><span class="sxs-lookup"><span data-stu-id="a99d9-164">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="a99d9-165">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="a99d9-165">Host configuration values</span></span>

<span data-ttu-id="a99d9-166">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) conta com as seguintes abordagens para definir os valores de configuração do host:</span><span class="sxs-lookup"><span data-stu-id="a99d9-166">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="a99d9-167">Configuração do construtor do host, que inclui variáveis de ambiente com o formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-167">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="a99d9-168">Por exemplo: `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-168">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="a99d9-169">Extensões como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) e [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (consulte a seção [Configuração de substituição](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="a99d9-169">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="a99d9-170">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e a chave associada.</span><span class="sxs-lookup"><span data-stu-id="a99d9-170">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="a99d9-171">Ao definir um valor com `UseSetting`, o valor é definido como uma cadeia de caracteres, independentemente do tipo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-171">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="a99d9-172">O host usa a opção que define um valor por último.</span><span class="sxs-lookup"><span data-stu-id="a99d9-172">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="a99d9-173">Para obter mais informações, veja [Substituir configuração](#override-configuration) na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="a99d9-173">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="a99d9-174">Chave do Aplicativo (Nome)</span><span class="sxs-lookup"><span data-stu-id="a99d9-174">Application Key (Name)</span></span>

<span data-ttu-id="a99d9-175">A propriedade [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="a99d9-175">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="a99d9-176">O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-176">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="a99d9-177">Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="a99d9-177">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

<span data-ttu-id="a99d9-178">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="a99d9-178">**Key**: applicationName</span></span>  
<span data-ttu-id="a99d9-179">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-179">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-180">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-180">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="a99d9-181">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a99d9-181">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a99d9-182">**Variável de ambiente**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="a99d9-182">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="a99d9-183">Capturar erros de inicialização</span><span class="sxs-lookup"><span data-stu-id="a99d9-183">Capture Startup Errors</span></span>

<span data-ttu-id="a99d9-184">Esta configuração controla a captura de erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="a99d9-184">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="a99d9-185">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="a99d9-185">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="a99d9-186">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a99d9-186">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a99d9-187">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-187">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="a99d9-188">**Definido usando**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="a99d9-188">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="a99d9-189">**Variável de ambiente**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="a99d9-189">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="a99d9-190">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="a99d9-190">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="a99d9-191">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="a99d9-191">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="a99d9-192">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="a99d9-192">Content Root</span></span>

<span data-ttu-id="a99d9-193">Essa configuração determina onde o ASP.NET Core começa a procurar por arquivos de conteúdo, como exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="a99d9-193">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="a99d9-194">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="a99d9-194">**Key**: contentRoot</span></span>  
<span data-ttu-id="a99d9-195">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-195">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-196">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="a99d9-196">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="a99d9-197">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="a99d9-197">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="a99d9-198">**Variável de ambiente**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="a99d9-198">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="a99d9-199">A raiz do conteúdo também é usada como o caminho base para a [Configuração da raiz da Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="a99d9-199">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="a99d9-200">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-200">If the path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a><span data-ttu-id="a99d9-201">Erros detalhados</span><span class="sxs-lookup"><span data-stu-id="a99d9-201">Detailed Errors</span></span>

<span data-ttu-id="a99d9-202">Determina se erros detalhados devem ser capturados.</span><span class="sxs-lookup"><span data-stu-id="a99d9-202">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="a99d9-203">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="a99d9-203">**Key**: detailedErrors</span></span>  
<span data-ttu-id="a99d9-204">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a99d9-204">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a99d9-205">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="a99d9-205">**Default**: false</span></span>  
<span data-ttu-id="a99d9-206">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a99d9-206">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a99d9-207">**Variável de ambiente**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="a99d9-207">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="a99d9-208">Quando habilitado (ou quando o <a href="#environment">Ambiente</a> é definido como `Development`), o aplicativo captura exceções detalhadas.</span><span class="sxs-lookup"><span data-stu-id="a99d9-208">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="a99d9-209">Ambiente</span><span class="sxs-lookup"><span data-stu-id="a99d9-209">Environment</span></span>

<span data-ttu-id="a99d9-210">Define o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-210">Sets the app's environment.</span></span>

<span data-ttu-id="a99d9-211">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="a99d9-211">**Key**: environment</span></span>  
<span data-ttu-id="a99d9-212">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-212">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-213">**Padrão**: Produção</span><span class="sxs-lookup"><span data-stu-id="a99d9-213">**Default**: Production</span></span>  
<span data-ttu-id="a99d9-214">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="a99d9-214">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="a99d9-215">**Variável de ambiente**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="a99d9-215">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="a99d9-216">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="a99d9-216">The environment can be set to any value.</span></span> <span data-ttu-id="a99d9-217">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a99d9-218">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="a99d9-218">Values aren't case sensitive.</span></span> <span data-ttu-id="a99d9-219">Por padrão, o *Ambiente* é lido da variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-219">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="a99d9-220">Ao usar o [Visual Studio](https://visualstudio.microsoft.com), variáveis de ambiente podem ser definidas no arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-220">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="a99d9-221">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-221">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="a99d9-222">Hospedando assemblies de inicialização</span><span class="sxs-lookup"><span data-stu-id="a99d9-222">Hosting Startup Assemblies</span></span>

<span data-ttu-id="a99d9-223">Define os assemblies de inicialização de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-223">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="a99d9-224">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="a99d9-224">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="a99d9-225">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-225">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-226">**Padrão**: Cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="a99d9-226">**Default**: Empty string</span></span>  
<span data-ttu-id="a99d9-227">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a99d9-227">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a99d9-228">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a99d9-228">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="a99d9-229">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a99d9-229">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="a99d9-230">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-230">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="a99d9-231">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="a99d9-231">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="a99d9-232">Porta HTTPS</span><span class="sxs-lookup"><span data-stu-id="a99d9-232">HTTPS Port</span></span>

<span data-ttu-id="a99d9-233">Defina a porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a99d9-233">Set the HTTPS redirect port.</span></span> <span data-ttu-id="a99d9-234">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="a99d9-234">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="a99d9-235">**Chave**: https_port **Tipo**: *cadeia de caracteres*
**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="a99d9-235">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="a99d9-236">**Definir usando**: `UseSetting`
**Variável de ambiente**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="a99d9-236">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="a99d9-237">Hospedando assemblies de exclusão de inicialização</span><span class="sxs-lookup"><span data-stu-id="a99d9-237">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="a99d9-238">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a99d9-238">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="a99d9-239">**Chave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="a99d9-239">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="a99d9-240">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-240">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-241">**Padrão**: Cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="a99d9-241">**Default**: Empty string</span></span>  
<span data-ttu-id="a99d9-242">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a99d9-242">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a99d9-243">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a99d9-243">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="a99d9-244">Preferir URLs de hospedagem</span><span class="sxs-lookup"><span data-stu-id="a99d9-244">Prefer Hosting URLs</span></span>

<span data-ttu-id="a99d9-245">Indica se o host deve escutar as URLs configuradas com o `WebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-245">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="a99d9-246">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="a99d9-246">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="a99d9-247">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a99d9-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a99d9-248">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="a99d9-248">**Default**: true</span></span>  
<span data-ttu-id="a99d9-249">**Definido usando**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="a99d9-249">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="a99d9-250">**Variável de ambiente**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="a99d9-250">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="a99d9-251">Impedir inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="a99d9-251">Prevent Hosting Startup</span></span>

<span data-ttu-id="a99d9-252">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-252">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="a99d9-253">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-253">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="a99d9-254">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a99d9-254">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="a99d9-255">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="a99d9-255">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a99d9-256">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="a99d9-256">**Default**: false</span></span>  
<span data-ttu-id="a99d9-257">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a99d9-257">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="a99d9-258">**Variável de ambiente**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="a99d9-258">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="a99d9-259">URLs de servidor</span><span class="sxs-lookup"><span data-stu-id="a99d9-259">Server URLs</span></span>

<span data-ttu-id="a99d9-260">Indica os endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="a99d9-260">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="a99d9-261">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="a99d9-261">**Key**: urls</span></span>  
<span data-ttu-id="a99d9-262">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-262">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-263">**Padrão**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="a99d9-263">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="a99d9-264">**Definido usando**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="a99d9-264">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="a99d9-265">**Variável de ambiente**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="a99d9-265">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="a99d9-266">Defina como uma lista separada por ponto e vírgula (;) de prefixos de URL aos quais o servidor deve responder.</span><span class="sxs-lookup"><span data-stu-id="a99d9-266">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="a99d9-267">Por exemplo: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-267">For example, `http://localhost:123`.</span></span> <span data-ttu-id="a99d9-268">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="a99d9-268">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="a99d9-269">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="a99d9-269">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="a99d9-270">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="a99d9-270">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="a99d9-271">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="a99d9-271">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="a99d9-272">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-272">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="a99d9-273">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="a99d9-273">Shutdown Timeout</span></span>

<span data-ttu-id="a99d9-274">Especifica o tempo de espera para o desligamento do host da Web.</span><span class="sxs-lookup"><span data-stu-id="a99d9-274">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="a99d9-275">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="a99d9-275">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="a99d9-276">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="a99d9-276">**Type**: *int*</span></span>  
<span data-ttu-id="a99d9-277">**Padrão**: 5</span><span class="sxs-lookup"><span data-stu-id="a99d9-277">**Default**: 5</span></span>  
<span data-ttu-id="a99d9-278">**Definido usando**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="a99d9-278">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="a99d9-279">**Variável de ambiente**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="a99d9-279">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="a99d9-280">Embora a chave aceite um *int* com `UseSetting` (por exemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), o método de extensão [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) usa um [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="a99d9-280">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="a99d9-281">Durante o período de tempo limite, a hospedagem:</span><span class="sxs-lookup"><span data-stu-id="a99d9-281">During the timeout period, hosting:</span></span>

* <span data-ttu-id="a99d9-282">Dispara [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="a99d9-282">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="a99d9-283">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="a99d9-283">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="a99d9-284">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-284">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="a99d9-285">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="a99d9-285">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="a99d9-286">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="a99d9-286">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="a99d9-287">Assembly de inicialização</span><span class="sxs-lookup"><span data-stu-id="a99d9-287">Startup Assembly</span></span>

<span data-ttu-id="a99d9-288">Determina o assembly para pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-288">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="a99d9-289">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="a99d9-289">**Key**: startupAssembly</span></span>  
<span data-ttu-id="a99d9-290">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-290">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-291">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a99d9-291">**Default**: The app's assembly</span></span>  
<span data-ttu-id="a99d9-292">**Definido usando**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="a99d9-292">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="a99d9-293">**Variável de ambiente**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="a99d9-293">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="a99d9-294">O assembly por nome (`string`) ou por tipo (`TStartup`) pode ser referenciado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-294">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="a99d9-295">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="a99d9-295">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="a99d9-296">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="a99d9-296">Web Root</span></span>

<span data-ttu-id="a99d9-297">Define o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-297">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="a99d9-298">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="a99d9-298">**Key**: webroot</span></span>  
<span data-ttu-id="a99d9-299">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a99d9-299">**Type**: *string*</span></span>  
<span data-ttu-id="a99d9-300">**Padrão**: se não for especificado, o padrão será "(Raiz do conteúdo)/wwwroot", se o caminho existir.</span><span class="sxs-lookup"><span data-stu-id="a99d9-300">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="a99d9-301">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-301">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="a99d9-302">**Definido usando**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="a99d9-302">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="a99d9-303">**Variável de ambiente**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="a99d9-303">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a><span data-ttu-id="a99d9-304">Substituir configuração</span><span class="sxs-lookup"><span data-stu-id="a99d9-304">Override configuration</span></span>

<span data-ttu-id="a99d9-305">Use [Configuração](xref:fundamentals/configuration/index) para configurar o host da Web.</span><span class="sxs-lookup"><span data-stu-id="a99d9-305">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="a99d9-306">No exemplo a seguir, a configuração do host é especificada, opcionalmente, em um arquivo *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a99d9-306">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="a99d9-307">Qualquer configuração carregada do arquivo *hostsettings.json* pode ser substituída por argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="a99d9-307">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="a99d9-308">A configuração de build (no `config`) é usada para configurar o host com [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="a99d9-308">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="a99d9-309">A configuração `IWebHostBuilder` é adicionada à configuração do aplicativo, mas o contrário não é verdade&mdash;`ConfigureAppConfiguration` não afeta a configuração `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-309">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="a99d9-310">Substituição da configuração fornecida por `UseUrls` pela configuração de *hostsettings.json* primeiro, e pela configuração de argumento da linha de comando depois:</span><span class="sxs-lookup"><span data-stu-id="a99d9-310">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="a99d9-311">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a99d9-311">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="a99d9-312">Atualmente, o método de extensão [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) não é capaz de analisar uma seção de configuração retornada por `GetSection` (por exemplo, `.UseConfiguration(Configuration.GetSection("section"))`).</span><span class="sxs-lookup"><span data-stu-id="a99d9-312">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="a99d9-313">O método `GetSection` filtra as chaves de configuração da seção solicitada, mas deixa o nome da seção nas chaves (por exemplo, `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="a99d9-313">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="a99d9-314">O método `UseConfiguration` espera que as chaves correspondam às chaves `WebHostBuilder` (por exemplo, `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="a99d9-314">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="a99d9-315">A presença do nome da seção nas chaves impede que os valores da seção configurem o host.</span><span class="sxs-lookup"><span data-stu-id="a99d9-315">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="a99d9-316">Esse problema será corrigido em uma próxima versão.</span><span class="sxs-lookup"><span data-stu-id="a99d9-316">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="a99d9-317">Para obter mais informações e soluções alternativas, consulte [Passar a seção de configuração para WebHostBuilder.UseConfiguration usa chaves completas](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="a99d9-317">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="a99d9-318">`UseConfiguration` somente copia as chaves do `IConfiguration` fornecido para a configuração do construtor de host.</span><span class="sxs-lookup"><span data-stu-id="a99d9-318">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="a99d9-319">Portanto, definir `reloadOnChange: true` para arquivos de configuração JSON, INI e XML não tem nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="a99d9-319">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="a99d9-320">Para especificar o host executado em uma URL específica, o valor desejado pode ser passado em um prompt de comando ao executar [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="a99d9-320">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="a99d9-321">O argumento de linha de comando substitui o valor `urls` do arquivo *hostsettings.json* e o servidor escuta na porta 8080:</span><span class="sxs-lookup"><span data-stu-id="a99d9-321">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="a99d9-322">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="a99d9-322">Manage the host</span></span>

<span data-ttu-id="a99d9-323">**Executar**</span><span class="sxs-lookup"><span data-stu-id="a99d9-323">**Run**</span></span>

<span data-ttu-id="a99d9-324">O método `Run` inicia o aplicativo Web e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="a99d9-324">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="a99d9-325">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="a99d9-325">**Start**</span></span>

<span data-ttu-id="a99d9-326">Execute o host sem bloqueio, chamando seu método `Start`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-326">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="a99d9-327">Se uma lista de URLs for passada para o método `Start`, ele escutará nas URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="a99d9-327">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="a99d9-328">O aplicativo pode inicializar e iniciar um novo host usando os padrões pré-configurados de `CreateDefaultBuilder`, usando um método estático conveniente.</span><span class="sxs-lookup"><span data-stu-id="a99d9-328">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="a99d9-329">Esses métodos iniciam o servidor sem uma saída do console e com [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) e aguardam uma quebra (Ctrl-C/SIGINT ou SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="a99d9-329">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="a99d9-330">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="a99d9-330">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="a99d9-331">Inicie com um `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-331">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a99d9-332">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="a99d9-332">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="a99d9-333">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="a99d9-333">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a99d9-334">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-334">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a99d9-335">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="a99d9-335">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="a99d9-336">Inicie com uma URL e `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-336">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a99d9-337">Produz o mesmo resultado que **Start(RequestDelegate app)** , mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-337">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="a99d9-338">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="a99d9-338">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="a99d9-339">Use uma instância de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar o middleware de roteamento:</span><span class="sxs-lookup"><span data-stu-id="a99d9-339">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a99d9-340">Use as seguintes solicitações de navegador com o exemplo:</span><span class="sxs-lookup"><span data-stu-id="a99d9-340">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="a99d9-341">Solicitação</span><span class="sxs-lookup"><span data-stu-id="a99d9-341">Request</span></span>                                    | <span data-ttu-id="a99d9-342">Resposta</span><span class="sxs-lookup"><span data-stu-id="a99d9-342">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="a99d9-343">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="a99d9-343">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="a99d9-344">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="a99d9-344">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="a99d9-345">Gera uma exceção com a cadeia de caracteres "ooops!"</span><span class="sxs-lookup"><span data-stu-id="a99d9-345">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="a99d9-346">Gera uma exceção com a cadeia de caracteres "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="a99d9-346">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="a99d9-347">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="a99d9-347">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="a99d9-348">Olá, Mundo!</span><span class="sxs-lookup"><span data-stu-id="a99d9-348">Hello World!</span></span>                             |

<span data-ttu-id="a99d9-349">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="a99d9-349">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a99d9-350">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-350">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a99d9-351">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="a99d9-351">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="a99d9-352">Use uma URL e uma instância de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-352">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a99d9-353">Produz o mesmo resultado que **Start(Action&lt;IRouteBuilder&gt; routeBuilder)** , mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-353">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="a99d9-354">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="a99d9-354">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="a99d9-355">Forneça um delegado para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-355">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a99d9-356">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="a99d9-356">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="a99d9-357">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="a99d9-357">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="a99d9-358">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-358">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="a99d9-359">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="a99d9-359">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="a99d9-360">Forneça um delegado e uma URL para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-360">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="a99d9-361">Produz o mesmo resultado que **StartWith(Action&lt;IApplicationBuilder&gt; app)** , mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-361">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="a99d9-362">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="a99d9-362">IHostingEnvironment interface</span></span>

<span data-ttu-id="a99d9-363">A [interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornece informações sobre o ambiente de hospedagem na Web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-363">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="a99d9-364">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="a99d9-364">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="a99d9-365">Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="a99d9-365">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="a99d9-366">Como alternativa, injete o `IHostingEnvironment` no construtor `Startup` para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a99d9-366">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="a99d9-367">Além do método de extensão `IsDevelopment`, `IHostingEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="a99d9-367">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="a99d9-368">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a99d9-368">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a99d9-369">O serviço `IHostingEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="a99d9-369">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="a99d9-370">`IHostingEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="a99d9-370">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="a99d9-371">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a99d9-371">IApplicationLifetime interface</span></span>

<span data-ttu-id="a99d9-372">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite atividades pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="a99d9-372">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="a99d9-373">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="a99d9-373">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="a99d9-374">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="a99d9-374">Cancellation Token</span></span>    | <span data-ttu-id="a99d9-375">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="a99d9-375">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="a99d9-376">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="a99d9-376">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="a99d9-377">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="a99d9-377">The host has fully started.</span></span> |
| [<span data-ttu-id="a99d9-378">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="a99d9-378">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="a99d9-379">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="a99d9-379">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="a99d9-380">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="a99d9-380">All requests should be processed.</span></span> <span data-ttu-id="a99d9-381">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="a99d9-381">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="a99d9-382">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="a99d9-382">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="a99d9-383">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="a99d9-383">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="a99d9-384">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="a99d9-384">Requests may still be processing.</span></span> <span data-ttu-id="a99d9-385">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="a99d9-385">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="a99d9-386">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a99d9-386">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="a99d9-387">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="a99d9-387">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="a99d9-388">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="a99d9-388">Scope validation</span></span>

<span data-ttu-id="a99d9-389">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) define [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` quando o ambiente do aplicativo é de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a99d9-389">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="a99d9-390">Quando `ValidateScopes` está definido como `true`, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="a99d9-390">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="a99d9-391">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="a99d9-391">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="a99d9-392">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="a99d9-392">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="a99d9-393">O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-393">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="a99d9-394">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-394">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="a99d9-395">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="a99d9-395">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="a99d9-396">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-396">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="a99d9-397">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="a99d9-397">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="a99d9-398">Para que os escopos sempre sejam validados, incluindo no ambiente de produção, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) com [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) no construtor do host:</span><span class="sxs-lookup"><span data-stu-id="a99d9-398">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="a99d9-399">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a99d9-399">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
