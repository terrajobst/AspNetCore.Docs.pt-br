---
title: Host da Web do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o host da Web no ASP.NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666708"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="dc990-103">Host da Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dc990-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="dc990-104">Aplicativos ASP.NET Core configuram e inicializam um *host*.</span><span class="sxs-lookup"><span data-stu-id="dc990-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="dc990-105">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="dc990-106">No mínimo, o host configura um servidor e um pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="dc990-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="dc990-107">O host também pode configurar registro em log, a injeção de dependência e a configuração.</span><span class="sxs-lookup"><span data-stu-id="dc990-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc990-108">Este artigo aborda o host da Web, que permanece disponível somente para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="dc990-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="dc990-109">O [Host genérico](xref:fundamentals/host/generic-host) é recomendado para todos os tipos de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dc990-110">Este artigo aborda o host da Web, que serve para hospedar aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="dc990-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="dc990-111">Para outros tipos de aplicativos, use o [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="dc990-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="dc990-112">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="dc990-112">Set up a host</span></span>

<span data-ttu-id="dc990-113">Crie um host usando uma instância do [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="dc990-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="dc990-114">Normalmente, isso é feito no ponto de entrada do aplicativo, o método `Main`.</span><span class="sxs-lookup"><span data-stu-id="dc990-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="dc990-115">Em modelos de projeto, `Main` está localizado em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="dc990-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="dc990-116">Um aplicativo típico chama [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para começar a configurar um host:</span><span class="sxs-lookup"><span data-stu-id="dc990-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="dc990-117">O código que chama `CreateDefaultBuilder` está em um método chamado `CreateWebHostBuilder`, que o separa do código em `Main`, que chama `Run` no objeto de construtor.</span><span class="sxs-lookup"><span data-stu-id="dc990-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="dc990-118">Essa separação será necessária se você usar [ferramentas do Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="dc990-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="dc990-119">As ferramentas esperam encontrar um método `CreateWebHostBuilder` que elas possam chamar em tempo de design para configurar o host sem executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="dc990-120">Uma alternativa é implementar `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="dc990-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="dc990-121">Para obter mais informações, confira [Criação de DbContext no tempo de design](/ef/core/miscellaneous/cli/dbcontext-creation).</span><span class="sxs-lookup"><span data-stu-id="dc990-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="dc990-122">`CreateDefaultBuilder` realiza as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="dc990-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="dc990-123">Configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="dc990-124">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="dc990-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="dc990-125">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) para o caminho retornado por [Directory. GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="dc990-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="dc990-126">Carrega a [configuração do host](#host-configuration-values) de:</span><span class="sxs-lookup"><span data-stu-id="dc990-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="dc990-127">Variáveis de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="dc990-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="dc990-128">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="dc990-128">Command-line arguments.</span></span>
* <span data-ttu-id="dc990-129">Carrega a configuração do aplicativo na seguinte ordem de:</span><span class="sxs-lookup"><span data-stu-id="dc990-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="dc990-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dc990-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="dc990-131">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="dc990-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="dc990-132">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="dc990-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="dc990-133">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="dc990-133">Environment variables.</span></span>
  * <span data-ttu-id="dc990-134">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="dc990-134">Command-line arguments.</span></span>
* <span data-ttu-id="dc990-135">Configura o [registro em log](xref:fundamentals/logging/index) para a saída do console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="dc990-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="dc990-136">O registro em log inclui regras de [filtragem de log](xref:fundamentals/logging/index#log-filtering) especificadas em uma seção de configuração de registro em log de um arquivo *appsettings.json* ou *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="dc990-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="dc990-137">Quando executado com a proteção do IIS com o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` habilita a [Integração do IIS](xref:host-and-deploy/iis/index), que configura a porta e o endereço básico do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="dc990-138">A Integração do IIS também configura o aplicativo para [capturar erros de inicialização](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="dc990-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="dc990-139">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="dc990-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="dc990-140">Definirá [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` se o ambiente do aplicativo for de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dc990-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="dc990-141">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="dc990-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="dc990-142">A configuração definida por `CreateDefaultBuilder` pode ser substituída e aumentada por [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) e outros métodos, bem como os métodos de extensão de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="dc990-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="dc990-143">Veja a seguir alguns exemplos:</span><span class="sxs-lookup"><span data-stu-id="dc990-143">A few examples follow:</span></span>

* <span data-ttu-id="dc990-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) é usado para especificar `IConfiguration` adicionais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="dc990-145">A seguinte chamada de `ConfigureAppConfiguration` adiciona um delegado para incluir a configuração do aplicativo no arquivo *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="dc990-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="dc990-146">`ConfigureAppConfiguration` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="dc990-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="dc990-147">Observe que essa configuração não se aplica ao host (por exemplo, URLs de servidor ou de ambiente).</span><span class="sxs-lookup"><span data-stu-id="dc990-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="dc990-148">Consulte a seção [Valores de configuração de Host](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="dc990-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="dc990-149">A seguinte chamada de `ConfigureLogging` adiciona um delegado para configurar o nível de log mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) como [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="dc990-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="dc990-150">Essa configuração substitui as configurações em *appsettings.Development.json* (`LogLevel.Debug`) e *appsettings.Production.json* (`LogLevel.Error`) configuradas por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dc990-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="dc990-151">`ConfigureLogging` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="dc990-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="dc990-152">A seguinte chamada para `ConfigureKestrel` substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 milhões de bytes, estabelecido quando o Kestrel foi configurado pelo `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="dc990-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="dc990-153">A seguinte chamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30.000.000 bytes estabelecido quando Kestrel foi configurado por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="dc990-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="dc990-154">A [raiz do conteúdo](xref:fundamentals/index#content-root) determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="dc990-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="dc990-155">Quando o aplicativo é iniciado na pasta raiz do projeto, essa pasta é usada como a raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="dc990-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="dc990-156">Esse é o padrão usado no [Visual Studio](https://visualstudio.microsoft.com) e nos [novos modelos dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="dc990-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="dc990-157">Para obter mais informações sobre a configuração de aplicativo, veja <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="dc990-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="dc990-158">Como uma alternativa ao uso do método `CreateDefaultBuilder` estático, criar um host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) é uma abordagem compatível com o ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="dc990-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="dc990-159">Ao configurar um host, os métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) podem ser fornecidos.</span><span class="sxs-lookup"><span data-stu-id="dc990-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="dc990-160">Se uma classe `Startup` for especificada, ela deverá definir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="dc990-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="dc990-161">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="dc990-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="dc990-162">Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="dc990-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="dc990-163">Diversas chamadas para `Configure` ou `UseStartup` no `WebHostBuilder` substituem configurações anteriores.</span><span class="sxs-lookup"><span data-stu-id="dc990-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="dc990-164">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="dc990-164">Host configuration values</span></span>

<span data-ttu-id="dc990-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) conta com as seguintes abordagens para definir os valores de configuração do host:</span><span class="sxs-lookup"><span data-stu-id="dc990-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="dc990-166">Configuração do construtor do host, que inclui variáveis de ambiente com o formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="dc990-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="dc990-167">Por exemplo, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="dc990-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="dc990-168">Extensões como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) e [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (consulte a seção [Configuração de substituição](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="dc990-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="dc990-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e a chave associada.</span><span class="sxs-lookup"><span data-stu-id="dc990-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="dc990-170">Ao definir um valor com `UseSetting`, o valor é definido como uma cadeia de caracteres, independentemente do tipo.</span><span class="sxs-lookup"><span data-stu-id="dc990-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="dc990-171">O host usa a opção que define um valor por último.</span><span class="sxs-lookup"><span data-stu-id="dc990-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="dc990-172">Para obter mais informações, veja [Substituir configuração](#override-configuration) na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="dc990-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="dc990-173">Chave do Aplicativo (Nome)</span><span class="sxs-lookup"><span data-stu-id="dc990-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc990-174">A propriedade `IWebHostEnvironment.ApplicationName` é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="dc990-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="dc990-175">O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="dc990-176">Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="dc990-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dc990-177">A propriedade [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="dc990-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="dc990-178">O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="dc990-179">Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="dc990-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="dc990-180">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="dc990-180">**Key**: applicationName</span></span>  
<span data-ttu-id="dc990-181">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-181">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-182">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="dc990-183">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="dc990-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="dc990-184">**Variável de ambiente**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="dc990-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="dc990-185">Capturar erros de inicialização</span><span class="sxs-lookup"><span data-stu-id="dc990-185">Capture Startup Errors</span></span>

<span data-ttu-id="dc990-186">Esta configuração controla a captura de erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc990-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="dc990-187">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="dc990-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="dc990-188">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="dc990-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="dc990-189">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="dc990-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="dc990-190">**Definido usando**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="dc990-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="dc990-191">**Variável de ambiente**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="dc990-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="dc990-192">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="dc990-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="dc990-193">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="dc990-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="dc990-194">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="dc990-194">Content root</span></span>

<span data-ttu-id="dc990-195">Essa configuração determina onde ASP.NET Core começa a Pesquisar arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="dc990-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="dc990-196">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="dc990-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="dc990-197">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-197">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-198">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="dc990-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="dc990-199">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="dc990-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="dc990-200">**Variável de ambiente**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="dc990-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="dc990-201">A raiz de conteúdo também é usada como o caminho base para a [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="dc990-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="dc990-202">Se o caminho raiz do conteúdo não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="dc990-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="dc990-203">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="dc990-203">For more information, see:</span></span>

* [<span data-ttu-id="dc990-204">Conceitos básicos: raiz de conteúdo</span><span class="sxs-lookup"><span data-stu-id="dc990-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="dc990-205">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="dc990-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="dc990-206">Erros detalhados</span><span class="sxs-lookup"><span data-stu-id="dc990-206">Detailed Errors</span></span>

<span data-ttu-id="dc990-207">Determina se erros detalhados devem ser capturados.</span><span class="sxs-lookup"><span data-stu-id="dc990-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="dc990-208">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="dc990-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="dc990-209">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="dc990-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="dc990-210">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="dc990-210">**Default**: false</span></span>  
<span data-ttu-id="dc990-211">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="dc990-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="dc990-212">**Variável de ambiente**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="dc990-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="dc990-213">Quando habilitado (ou quando o <a href="#environment">Ambiente</a> é definido como `Development`), o aplicativo captura exceções detalhadas.</span><span class="sxs-lookup"><span data-stu-id="dc990-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="dc990-214">Ambiente</span><span class="sxs-lookup"><span data-stu-id="dc990-214">Environment</span></span>

<span data-ttu-id="dc990-215">Define o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-215">Sets the app's environment.</span></span>

<span data-ttu-id="dc990-216">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="dc990-216">**Key**: environment</span></span>  
<span data-ttu-id="dc990-217">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-217">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-218">**Padrão**: Production</span><span class="sxs-lookup"><span data-stu-id="dc990-218">**Default**: Production</span></span>  
<span data-ttu-id="dc990-219">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="dc990-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="dc990-220">**Variável de ambiente**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="dc990-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="dc990-221">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="dc990-221">The environment can be set to any value.</span></span> <span data-ttu-id="dc990-222">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="dc990-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="dc990-223">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="dc990-223">Values aren't case sensitive.</span></span> <span data-ttu-id="dc990-224">Por padrão, o *Ambiente* é lido da variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="dc990-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="dc990-225">Ao usar o [Visual Studio](https://visualstudio.microsoft.com), variáveis de ambiente podem ser definidas no arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dc990-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="dc990-226">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="dc990-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="dc990-227">Hospedando assemblies de inicialização</span><span class="sxs-lookup"><span data-stu-id="dc990-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="dc990-228">Define os assemblies de inicialização de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="dc990-229">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="dc990-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="dc990-230">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-230">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-231">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="dc990-231">**Default**: Empty string</span></span>  
<span data-ttu-id="dc990-232">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="dc990-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="dc990-233">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="dc990-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="dc990-234">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc990-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="dc990-235">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="dc990-236">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc990-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="dc990-237">Porta HTTPS</span><span class="sxs-lookup"><span data-stu-id="dc990-237">HTTPS Port</span></span>

<span data-ttu-id="dc990-238">Defina a porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="dc990-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="dc990-239">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="dc990-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="dc990-240">**Chave**: https_port **Tipo**: *string*
**Padrão**: Um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="dc990-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="dc990-241">**Definido usando**: `UseSetting`
**Variável de ambiente**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="dc990-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="dc990-242">Hospedando assemblies de exclusão de inicialização</span><span class="sxs-lookup"><span data-stu-id="dc990-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="dc990-243">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc990-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="dc990-244">**Chave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="dc990-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="dc990-245">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-245">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-246">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="dc990-246">**Default**: Empty string</span></span>  
<span data-ttu-id="dc990-247">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="dc990-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="dc990-248">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="dc990-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="dc990-249">Preferir URLs de hospedagem</span><span class="sxs-lookup"><span data-stu-id="dc990-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="dc990-250">Indica se o host deve escutar as URLs configuradas com o `WebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="dc990-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="dc990-251">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="dc990-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="dc990-252">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="dc990-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="dc990-253">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="dc990-253">**Default**: true</span></span>  
<span data-ttu-id="dc990-254">**Definido usando**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="dc990-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="dc990-255">**Variável de ambiente**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="dc990-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="dc990-256">Impedir inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="dc990-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="dc990-257">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="dc990-258">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dc990-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="dc990-259">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="dc990-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="dc990-260">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="dc990-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="dc990-261">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="dc990-261">**Default**: false</span></span>  
<span data-ttu-id="dc990-262">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="dc990-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="dc990-263">**Variável de ambiente**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="dc990-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="dc990-264">URLs de servidor</span><span class="sxs-lookup"><span data-stu-id="dc990-264">Server URLs</span></span>

<span data-ttu-id="dc990-265">Indica os endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="dc990-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="dc990-266">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="dc990-266">**Key**: urls</span></span>  
<span data-ttu-id="dc990-267">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-267">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-268">**Padrão**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="dc990-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="dc990-269">**Definido usando**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="dc990-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="dc990-270">**Variável de ambiente**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="dc990-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="dc990-271">Defina como uma lista separada por ponto e vírgula (;) de prefixos de URL aos quais o servidor deve responder.</span><span class="sxs-lookup"><span data-stu-id="dc990-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="dc990-272">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="dc990-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="dc990-273">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="dc990-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="dc990-274">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="dc990-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="dc990-275">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="dc990-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="dc990-276">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="dc990-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="dc990-277">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dc990-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="dc990-278">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="dc990-278">Shutdown Timeout</span></span>

<span data-ttu-id="dc990-279">Especifica o tempo de espera para o desligamento do host da Web.</span><span class="sxs-lookup"><span data-stu-id="dc990-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="dc990-280">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="dc990-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="dc990-281">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="dc990-281">**Type**: *int*</span></span>  
<span data-ttu-id="dc990-282">**Padrão**: 5</span><span class="sxs-lookup"><span data-stu-id="dc990-282">**Default**: 5</span></span>  
<span data-ttu-id="dc990-283">**Definido usando**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="dc990-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="dc990-284">**Variável de ambiente**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="dc990-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="dc990-285">Embora a chave aceite um *int* com `UseSetting` (por exemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), o método de extensão [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) usa um [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="dc990-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="dc990-286">Durante o período de tempo limite, a hospedagem:</span><span class="sxs-lookup"><span data-stu-id="dc990-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="dc990-287">Dispara [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="dc990-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="dc990-288">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="dc990-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="dc990-289">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="dc990-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="dc990-290">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="dc990-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="dc990-291">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="dc990-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="dc990-292">Assembly de inicialização</span><span class="sxs-lookup"><span data-stu-id="dc990-292">Startup Assembly</span></span>

<span data-ttu-id="dc990-293">Determina o assembly para pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="dc990-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="dc990-294">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="dc990-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="dc990-295">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-295">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-296">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="dc990-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="dc990-297">**Definido usando**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="dc990-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="dc990-298">**Variável de ambiente**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="dc990-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="dc990-299">O assembly por nome (`string`) ou por tipo (`TStartup`) pode ser referenciado.</span><span class="sxs-lookup"><span data-stu-id="dc990-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="dc990-300">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="dc990-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="dc990-301">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="dc990-301">Web root</span></span>

<span data-ttu-id="dc990-302">Define o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="dc990-303">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="dc990-303">**Key**: webroot</span></span>  
<span data-ttu-id="dc990-304">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="dc990-304">**Type**: *string*</span></span>  
<span data-ttu-id="dc990-305">**Padrão**: o padrão é `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="dc990-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="dc990-306">O caminho para *{Content root}/wwwroot* deve existir.</span><span class="sxs-lookup"><span data-stu-id="dc990-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="dc990-307">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="dc990-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="dc990-308">**Definido usando**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="dc990-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="dc990-309">**Variável de ambiente**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="dc990-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="dc990-310">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="dc990-310">For more information, see:</span></span>

* [<span data-ttu-id="dc990-311">Conceitos básicos: raiz da Web</span><span class="sxs-lookup"><span data-stu-id="dc990-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="dc990-312">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="dc990-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="dc990-313">Substituir configuração</span><span class="sxs-lookup"><span data-stu-id="dc990-313">Override configuration</span></span>

<span data-ttu-id="dc990-314">Use [Configuração](xref:fundamentals/configuration/index) para configurar o host da Web.</span><span class="sxs-lookup"><span data-stu-id="dc990-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="dc990-315">No exemplo a seguir, a configuração do host é especificada, opcionalmente, em um arquivo *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="dc990-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="dc990-316">Qualquer configuração carregada do arquivo *hostsettings.json* pode ser substituída por argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="dc990-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="dc990-317">A configuração de build (no `config`) é usada para configurar o host com [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="dc990-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="dc990-318">A configuração `IWebHostBuilder` é adicionada à configuração do aplicativo, mas o contrário não é verdade&mdash;`ConfigureAppConfiguration` não afeta a configuração `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="dc990-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="dc990-319">Substituição da configuração fornecida por `UseUrls` pela configuração de *hostsettings.json* primeiro, e pela configuração de argumento da linha de comando depois:</span><span class="sxs-lookup"><span data-stu-id="dc990-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="dc990-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="dc990-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="dc990-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) copia apenas as chaves do `IConfiguration` fornecido para a configuração do host Builder.</span><span class="sxs-lookup"><span data-stu-id="dc990-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="dc990-322">Portanto, definir `reloadOnChange: true` para arquivos de configuração JSON, INI e XML não tem nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="dc990-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="dc990-323">Para especificar o host executado em uma URL específica, o valor desejado pode ser passado em um prompt de comando ao executar [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="dc990-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="dc990-324">O argumento de linha de comando substitui o valor `urls` do arquivo *hostsettings.json* e o servidor escuta na porta 8080:</span><span class="sxs-lookup"><span data-stu-id="dc990-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="dc990-325">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="dc990-325">Manage the host</span></span>

<span data-ttu-id="dc990-326">**Executar**</span><span class="sxs-lookup"><span data-stu-id="dc990-326">**Run**</span></span>

<span data-ttu-id="dc990-327">O método `Run` inicia o aplicativo Web e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="dc990-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="dc990-328">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="dc990-328">**Start**</span></span>

<span data-ttu-id="dc990-329">Execute o host sem bloqueio, chamando seu método `Start`:</span><span class="sxs-lookup"><span data-stu-id="dc990-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="dc990-330">Se uma lista de URLs for passada para o método `Start`, ele escutará nas URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="dc990-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="dc990-331">O aplicativo pode inicializar e iniciar um novo host usando os padrões pré-configurados de `CreateDefaultBuilder`, usando um método estático conveniente.</span><span class="sxs-lookup"><span data-stu-id="dc990-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="dc990-332">Esses métodos iniciam o servidor sem uma saída do console e com [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) e aguardam uma quebra (Ctrl-C/SIGINT ou SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="dc990-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="dc990-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="dc990-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="dc990-334">Inicie com um `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="dc990-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="dc990-335">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="dc990-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="dc990-336">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="dc990-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="dc990-337">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="dc990-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="dc990-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="dc990-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="dc990-339">Inicie com uma URL e `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="dc990-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="dc990-340">Produz o mesmo resultado que **Start(RequestDelegate app)** , mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="dc990-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="dc990-341">**Start (ação\<IRouteBuilder > routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="dc990-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="dc990-342">Use uma instância de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar o middleware de roteamento:</span><span class="sxs-lookup"><span data-stu-id="dc990-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="dc990-343">Use as seguintes solicitações de navegador com o exemplo:</span><span class="sxs-lookup"><span data-stu-id="dc990-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="dc990-344">Solicitação</span><span class="sxs-lookup"><span data-stu-id="dc990-344">Request</span></span>                                    | <span data-ttu-id="dc990-345">Resposta</span><span class="sxs-lookup"><span data-stu-id="dc990-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="dc990-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="dc990-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="dc990-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="dc990-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="dc990-348">Gera uma exceção com a cadeia de caracteres "ooops!"</span><span class="sxs-lookup"><span data-stu-id="dc990-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="dc990-349">Gera uma exceção com a cadeia de caracteres "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="dc990-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="dc990-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="dc990-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="dc990-351">Olá, Mundo!</span><span class="sxs-lookup"><span data-stu-id="dc990-351">Hello World!</span></span>                             |

<span data-ttu-id="dc990-352">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="dc990-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="dc990-353">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="dc990-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="dc990-354">**Start (cadeia de caracteres URL, ação\<IRouteBuilder > routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="dc990-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="dc990-355">Use uma URL e uma instância de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="dc990-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="dc990-356">Produz o mesmo resultado que **Start (ação\<IRouteBuilder > routeBuilder)** , exceto que o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="dc990-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="dc990-357">**StartWith (ação\<IApplicationBuilder > aplicativo)**</span><span class="sxs-lookup"><span data-stu-id="dc990-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="dc990-358">Forneça um delegado para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="dc990-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="dc990-359">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="dc990-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="dc990-360">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="dc990-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="dc990-361">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="dc990-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="dc990-362">**StartWith (URL de cadeia de caracteres, ação\<IApplicationBuilder > aplicativo)**</span><span class="sxs-lookup"><span data-stu-id="dc990-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="dc990-363">Forneça um delegado e uma URL para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="dc990-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="dc990-364">Produz o mesmo resultado que **StartWith (Action\<IApplicationBuilder > app)** , exceto que o aplicativo responde `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="dc990-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="dc990-365">Interface IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="dc990-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="dc990-366">A interface `IWebHostEnvironment` fornece informações sobre o ambiente de hospedagem na Web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="dc990-367">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IWebHostEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="dc990-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
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

<span data-ttu-id="dc990-368">Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="dc990-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="dc990-369">Como alternativa, injete o `IWebHostEnvironment` no construtor `Startup` para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="dc990-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

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
> <span data-ttu-id="dc990-370">Além do método de extensão `IsDevelopment`, `IWebHostEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="dc990-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="dc990-371">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="dc990-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="dc990-372">O serviço `IWebHostEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="dc990-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

<span data-ttu-id="dc990-373">`IWebHostEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="dc990-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="dc990-374">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="dc990-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="dc990-375">A [interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornece informações sobre o ambiente de hospedagem na Web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="dc990-376">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="dc990-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="dc990-377">Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="dc990-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="dc990-378">Como alternativa, injete o `IHostingEnvironment` no construtor `Startup` para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="dc990-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="dc990-379">Além do método de extensão `IsDevelopment`, `IHostingEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="dc990-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="dc990-380">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="dc990-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="dc990-381">O serviço `IHostingEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="dc990-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="dc990-382">`IHostingEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/write) personalizado:</span><span class="sxs-lookup"><span data-stu-id="dc990-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="dc990-383">Interface IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="dc990-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="dc990-384">`IHostApplicationLifetime` permite atividades de pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="dc990-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="dc990-385">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="dc990-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="dc990-386">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="dc990-386">Cancellation Token</span></span>    | <span data-ttu-id="dc990-387">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="dc990-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="dc990-388">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="dc990-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="dc990-389">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="dc990-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="dc990-390">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="dc990-390">All requests should be processed.</span></span> <span data-ttu-id="dc990-391">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="dc990-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="dc990-392">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="dc990-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="dc990-393">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="dc990-393">Requests may still be processing.</span></span> <span data-ttu-id="dc990-394">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="dc990-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
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

<span data-ttu-id="dc990-395">`StopApplication` solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="dc990-396">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="dc990-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="dc990-397">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="dc990-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="dc990-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite atividades pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="dc990-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="dc990-399">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="dc990-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="dc990-400">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="dc990-400">Cancellation Token</span></span>    | <span data-ttu-id="dc990-401">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="dc990-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="dc990-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="dc990-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="dc990-403">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="dc990-403">The host has fully started.</span></span> |
| [<span data-ttu-id="dc990-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="dc990-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="dc990-405">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="dc990-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="dc990-406">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="dc990-406">All requests should be processed.</span></span> <span data-ttu-id="dc990-407">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="dc990-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="dc990-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="dc990-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="dc990-409">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="dc990-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="dc990-410">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="dc990-410">Requests may still be processing.</span></span> <span data-ttu-id="dc990-411">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="dc990-411">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="dc990-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dc990-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="dc990-413">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="dc990-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="dc990-414">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="dc990-414">Scope validation</span></span>

<span data-ttu-id="dc990-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) define [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` quando o ambiente do aplicativo é de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dc990-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="dc990-416">Quando `ValidateScopes` está definido como `true`, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="dc990-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="dc990-417">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="dc990-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="dc990-418">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="dc990-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="dc990-419">O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado.</span><span class="sxs-lookup"><span data-stu-id="dc990-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="dc990-420">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="dc990-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="dc990-421">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="dc990-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="dc990-422">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="dc990-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="dc990-423">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="dc990-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="dc990-424">Para que os escopos sempre sejam validados, incluindo no ambiente de produção, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) com [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) no construtor do host:</span><span class="sxs-lookup"><span data-stu-id="dc990-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="dc990-425">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dc990-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
