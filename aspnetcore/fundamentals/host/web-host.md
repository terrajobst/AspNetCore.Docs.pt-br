---
title: Host da Web do ASP.NET Core
author: guardrex
description: Saiba mais sobre o host da Web no ASP.NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: fundamentals/host/web-host
ms.openlocfilehash: bc77413127273aba207e68e7fbcb8ad916267e8e
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862272"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="4702a-103">Host da Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4702a-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="4702a-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4702a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="4702a-105">Para obter a versão 1.1 deste tópico, baixe [Host da Web do ASP.NET Core (versão 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="4702a-105">For the 1.1 version of this topic, download [ASP.NET Core Web Host (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Web-Host_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="4702a-106">Aplicativos ASP.NET Core configuram e inicializam um *host*.</span><span class="sxs-lookup"><span data-stu-id="4702a-106">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="4702a-107">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-107">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4702a-108">No mínimo, o host configura um servidor e um pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="4702a-108">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="4702a-109">Este tópico aborda o Host da Web ASP.NET Core ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)), que é útil para hospedagem de aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="4702a-109">This topic covers the ASP.NET Core Web Host ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)), which is useful for hosting web apps.</span></span> <span data-ttu-id="4702a-110">Para cobertura do Host Genérico .NET ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)), veja <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="4702a-110">For coverage of the .NET Generic Host ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)), see <xref:fundamentals/host/generic-host>.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4702a-111">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="4702a-111">Set up a host</span></span>

<span data-ttu-id="4702a-112">Crie um host usando uma instância do [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="4702a-112">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="4702a-113">Normalmente, isso é feito no ponto de entrada do aplicativo, o método `Main`.</span><span class="sxs-lookup"><span data-stu-id="4702a-113">This is typically performed in the app's entry point, the `Main` method.</span></span> <span data-ttu-id="4702a-114">Em modelos de projeto, `Main` está localizado em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4702a-114">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="4702a-115">Um *Program.cs* típico chama [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para começar a configurar um host:</span><span class="sxs-lookup"><span data-stu-id="4702a-115">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="4702a-116">`CreateDefaultBuilder` executa as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="4702a-116">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="4702a-117">Configura o servidor [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web usando provedores de configuração de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-117">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="4702a-118">Para obter as opções padrão do servidor Kestrel, confira <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="4702a-118">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4702a-119">Define a raiz do conteúdo como o caminho retornado por [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="4702a-119">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="4702a-120">Carrega a [configuração do host](#host-configuration-values) de:</span><span class="sxs-lookup"><span data-stu-id="4702a-120">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="4702a-121">Variáveis de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="4702a-121">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="4702a-122">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4702a-122">Command-line arguments.</span></span>
* <span data-ttu-id="4702a-123">Carrega a configuração do aplicativo na seguinte ordem de:</span><span class="sxs-lookup"><span data-stu-id="4702a-123">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="4702a-124">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4702a-124">*appsettings.json*.</span></span>
  * <span data-ttu-id="4702a-125">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4702a-125">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4702a-126">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="4702a-126">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="4702a-127">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="4702a-127">Environment variables.</span></span>
  * <span data-ttu-id="4702a-128">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4702a-128">Command-line arguments.</span></span>
* <span data-ttu-id="4702a-129">Configura o [registro em log](xref:fundamentals/logging/index) para a saída do console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="4702a-129">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="4702a-130">O registro em log inclui regras de [filtragem de log](xref:fundamentals/logging/index#log-filtering) especificadas em uma seção de configuração de registro em log de um arquivo *appsettings.json* ou *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4702a-130">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="4702a-131">Quando executado com a proteção do IIS com o [Módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module), `CreateDefaultBuilder` habilita a [Integração do IIS](xref:host-and-deploy/iis/index), que configura a porta e o endereço básico do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-131">When running behind IIS with the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="4702a-132">A Integração do IIS também configura o aplicativo para [capturar erros de inicialização](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="4702a-132">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="4702a-133">Para as opções padrão do IIS, veja <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="4702a-133">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="4702a-134">Definirá [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` se o ambiente do aplicativo for de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="4702a-134">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="4702a-135">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="4702a-135">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="4702a-136">A configuração definida por `CreateDefaultBuilder` pode ser substituída e aumentada por [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) e outros métodos, bem como os métodos de extensão de [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="4702a-136">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="4702a-137">Veja a seguir alguns exemplos:</span><span class="sxs-lookup"><span data-stu-id="4702a-137">A few examples follow:</span></span>

* <span data-ttu-id="4702a-138">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) é usado para especificar `IConfiguration` adicionais para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-138">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="4702a-139">A seguinte chamada de `ConfigureAppConfiguration` adiciona um delegado para incluir a configuração do aplicativo no arquivo *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="4702a-139">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="4702a-140">`ConfigureAppConfiguration` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="4702a-140">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="4702a-141">Observe que essa configuração não se aplica ao host (por exemplo, URLs de servidor ou de ambiente).</span><span class="sxs-lookup"><span data-stu-id="4702a-141">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="4702a-142">Consulte a seção [Valores de configuração de Host](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="4702a-142">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="4702a-143">A seguinte chamada de `ConfigureLogging` adiciona um delegado para configurar o nível de log mínimo ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) como [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="4702a-143">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="4702a-144">Essa configuração substitui as configurações em *appsettings.Development.json* (`LogLevel.Debug`) e *appsettings.Production.json* (`LogLevel.Error`) configuradas por `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4702a-144">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4702a-145">`ConfigureLogging` pode ser chamado várias vezes.</span><span class="sxs-lookup"><span data-stu-id="4702a-145">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="4702a-146">A seguinte chamada para `ConfigureKestrel` substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30 milhões de bytes, estabelecido quando o Kestrel foi configurado pelo `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4702a-146">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="4702a-147">A seguinte chamada a [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) substitui o padrão [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) de 30.000.000 bytes estabelecido quando Kestrel foi configurado por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4702a-147">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="4702a-148">A *raiz do conteúdo* determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="4702a-148">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="4702a-149">Quando o aplicativo é iniciado na pasta raiz do projeto, essa pasta é usada como a raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="4702a-149">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="4702a-150">Esse é o padrão usado no [Visual Studio](https://www.visualstudio.com/) e nos [novos modelos dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="4702a-150">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="4702a-151">Para obter mais informações sobre a configuração de aplicativo, veja <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4702a-151">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="4702a-152">Como uma alternativa ao uso do método `CreateDefaultBuilder` estático, criar um host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) é uma abordagem compatível com o ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="4702a-152">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="4702a-153">Para obter mais informações, consulte a guia do ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="4702a-153">For more information, see the ASP.NET Core 1.x tab.</span></span>

<span data-ttu-id="4702a-154">Ao configurar um host, os métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) podem ser fornecidos.</span><span class="sxs-lookup"><span data-stu-id="4702a-154">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods can be provided.</span></span> <span data-ttu-id="4702a-155">Se uma classe `Startup` for especificada, ela deverá definir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="4702a-155">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="4702a-156">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4702a-156">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="4702a-157">Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="4702a-157">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="4702a-158">Diversas chamadas para `Configure` ou `UseStartup` no `WebHostBuilder` substituem configurações anteriores.</span><span class="sxs-lookup"><span data-stu-id="4702a-158">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="4702a-159">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="4702a-159">Host configuration values</span></span>

<span data-ttu-id="4702a-160">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) conta com as seguintes abordagens para definir os valores de configuração do host:</span><span class="sxs-lookup"><span data-stu-id="4702a-160">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="4702a-161">Configuração do construtor do host, que inclui variáveis de ambiente com o formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="4702a-161">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="4702a-162">Por exemplo, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="4702a-162">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="4702a-163">Extensões como [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) e [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (consulte a seção [Configuração de substituição](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="4702a-163">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="4702a-164">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e a chave associada.</span><span class="sxs-lookup"><span data-stu-id="4702a-164">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="4702a-165">Ao definir um valor com `UseSetting`, o valor é definido como uma cadeia de caracteres, independentemente do tipo.</span><span class="sxs-lookup"><span data-stu-id="4702a-165">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="4702a-166">O host usa a opção que define um valor por último.</span><span class="sxs-lookup"><span data-stu-id="4702a-166">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="4702a-167">Para obter mais informações, veja [Substituir configuração](#override-configuration) na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="4702a-167">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4702a-168">Chave do Aplicativo (Nome)</span><span class="sxs-lookup"><span data-stu-id="4702a-168">Application Key (Name)</span></span>

<span data-ttu-id="4702a-169">A propriedade [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) é definida automaticamente quando [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) ou [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) é chamado durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="4702a-169">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="4702a-170">O valor é definido para o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-170">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="4702a-171">Para definir o valor explicitamente, use o [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span><span class="sxs-lookup"><span data-stu-id="4702a-171">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

<span data-ttu-id="4702a-172">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="4702a-172">**Key**: applicationName</span></span>  
<span data-ttu-id="4702a-173">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-173">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-174">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-174">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4702a-175">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4702a-175">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4702a-176">**Variável de ambiente**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4702a-176">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="4702a-177">Capturar erros de inicialização</span><span class="sxs-lookup"><span data-stu-id="4702a-177">Capture Startup Errors</span></span>

<span data-ttu-id="4702a-178">Esta configuração controla a captura de erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="4702a-178">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="4702a-179">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4702a-179">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="4702a-180">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="4702a-180">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4702a-181">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="4702a-181">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4702a-182">**Definido usando**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4702a-182">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="4702a-183">**Variável de ambiente**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4702a-183">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4702a-184">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="4702a-184">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4702a-185">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="4702a-185">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="4702a-186">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="4702a-186">Content Root</span></span>

<span data-ttu-id="4702a-187">Essa configuração determina onde o ASP.NET Core começa a procurar por arquivos de conteúdo, como exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="4702a-187">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="4702a-188">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="4702a-188">**Key**: contentRoot</span></span>  
<span data-ttu-id="4702a-189">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-189">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-190">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="4702a-190">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4702a-191">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4702a-191">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4702a-192">**Variável de ambiente**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4702a-192">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="4702a-193">A raiz do conteúdo também é usada como o caminho base para a [Configuração da raiz da Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="4702a-193">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="4702a-194">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="4702a-194">If the path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

### <a name="detailed-errors"></a><span data-ttu-id="4702a-195">Erros detalhados</span><span class="sxs-lookup"><span data-stu-id="4702a-195">Detailed Errors</span></span>

<span data-ttu-id="4702a-196">Determina se erros detalhados devem ser capturados.</span><span class="sxs-lookup"><span data-stu-id="4702a-196">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="4702a-197">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="4702a-197">**Key**: detailedErrors</span></span>  
<span data-ttu-id="4702a-198">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="4702a-198">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4702a-199">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="4702a-199">**Default**: false</span></span>  
<span data-ttu-id="4702a-200">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4702a-200">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4702a-201">**Variável de ambiente**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4702a-201">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="4702a-202">Quando habilitado (ou quando o <a href="#environment">Ambiente</a> é definido como `Development`), o aplicativo captura exceções detalhadas.</span><span class="sxs-lookup"><span data-stu-id="4702a-202">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="4702a-203">Ambiente</span><span class="sxs-lookup"><span data-stu-id="4702a-203">Environment</span></span>

<span data-ttu-id="4702a-204">Define o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-204">Sets the app's environment.</span></span>

<span data-ttu-id="4702a-205">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="4702a-205">**Key**: environment</span></span>  
<span data-ttu-id="4702a-206">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-206">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-207">**Padrão**: Production</span><span class="sxs-lookup"><span data-stu-id="4702a-207">**Default**: Production</span></span>  
<span data-ttu-id="4702a-208">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4702a-208">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4702a-209">**Variável de ambiente**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4702a-209">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="4702a-210">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="4702a-210">The environment can be set to any value.</span></span> <span data-ttu-id="4702a-211">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="4702a-211">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4702a-212">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4702a-212">Values aren't case sensitive.</span></span> <span data-ttu-id="4702a-213">Por padrão, o *Ambiente* é lido da variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="4702a-213">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="4702a-214">Ao usar o [Visual Studio](https://www.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4702a-214">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="4702a-215">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4702a-215">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="4702a-216">Hospedando assemblies de inicialização</span><span class="sxs-lookup"><span data-stu-id="4702a-216">Hosting Startup Assemblies</span></span>

<span data-ttu-id="4702a-217">Define os assemblies de inicialização de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-217">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="4702a-218">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4702a-218">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="4702a-219">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-219">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-220">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="4702a-220">**Default**: Empty string</span></span>  
<span data-ttu-id="4702a-221">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4702a-221">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4702a-222">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4702a-222">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4702a-223">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="4702a-223">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="4702a-224">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-224">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4702a-225">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="4702a-225">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="4702a-226">Porta HTTPS</span><span class="sxs-lookup"><span data-stu-id="4702a-226">HTTPS Port</span></span>

<span data-ttu-id="4702a-227">Defina a porta de redirecionamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4702a-227">Set the HTTPS redirect port.</span></span> <span data-ttu-id="4702a-228">Uso em [aplicação de HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="4702a-228">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4702a-229">**Chave**: https_port **Tipo**: *string*
**Padrão**: Um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="4702a-229">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="4702a-230">**Definido usando**: `UseSetting`
**Variável de ambiente**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4702a-230">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="4702a-231">Hospedando assemblies de exclusão de inicialização</span><span class="sxs-lookup"><span data-stu-id="4702a-231">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="4702a-232">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para exclusão na inicialização.</span><span class="sxs-lookup"><span data-stu-id="4702a-232">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4702a-233">**Chave**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4702a-233">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="4702a-234">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-234">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-235">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="4702a-235">**Default**: Empty string</span></span>  
<span data-ttu-id="4702a-236">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4702a-236">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4702a-237">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4702a-237">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="4702a-238">Preferir URLs de hospedagem</span><span class="sxs-lookup"><span data-stu-id="4702a-238">Prefer Hosting URLs</span></span>

<span data-ttu-id="4702a-239">Indica se o host deve escutar as URLs configuradas com o `WebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="4702a-239">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4702a-240">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4702a-240">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="4702a-241">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="4702a-241">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4702a-242">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="4702a-242">**Default**: true</span></span>  
<span data-ttu-id="4702a-243">**Definido usando**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4702a-243">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="4702a-244">**Variável de ambiente**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4702a-244">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="4702a-245">Impedir inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="4702a-245">Prevent Hosting Startup</span></span>

<span data-ttu-id="4702a-246">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-246">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4702a-247">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4702a-247">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4702a-248">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4702a-248">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="4702a-249">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="4702a-249">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="4702a-250">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="4702a-250">**Default**: false</span></span>  
<span data-ttu-id="4702a-251">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="4702a-251">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="4702a-252">**Variável de ambiente**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4702a-252">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="4702a-253">URLs de servidor</span><span class="sxs-lookup"><span data-stu-id="4702a-253">Server URLs</span></span>

<span data-ttu-id="4702a-254">Indica os endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="4702a-254">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="4702a-255">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="4702a-255">**Key**: urls</span></span>  
<span data-ttu-id="4702a-256">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-256">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-257">**Padrão**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="4702a-257">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="4702a-258">**Definido usando**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="4702a-258">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="4702a-259">**Variável de ambiente**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="4702a-259">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="4702a-260">Defina como uma lista separada por ponto e vírgula (;) de prefixos de URL aos quais o servidor deve responder.</span><span class="sxs-lookup"><span data-stu-id="4702a-260">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="4702a-261">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="4702a-261">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4702a-262">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="4702a-262">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4702a-263">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="4702a-263">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4702a-264">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="4702a-264">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="4702a-265">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="4702a-265">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4702a-266">Para obter mais informações, consulte <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4702a-266">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4702a-267">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="4702a-267">Shutdown Timeout</span></span>

<span data-ttu-id="4702a-268">Especifica o tempo de espera para o desligamento do host da Web.</span><span class="sxs-lookup"><span data-stu-id="4702a-268">Specifies the amount of time to wait for the web host to shut down.</span></span>

<span data-ttu-id="4702a-269">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="4702a-269">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="4702a-270">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="4702a-270">**Type**: *int*</span></span>  
<span data-ttu-id="4702a-271">**Padrão**: 5</span><span class="sxs-lookup"><span data-stu-id="4702a-271">**Default**: 5</span></span>  
<span data-ttu-id="4702a-272">**Definido usando**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="4702a-272">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="4702a-273">**Variável de ambiente**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4702a-273">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4702a-274">Embora a chave aceite um *int* com `UseSetting` (por exemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), o método de extensão [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) usa um [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="4702a-274">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="4702a-275">Durante o período de tempo limite, a hospedagem:</span><span class="sxs-lookup"><span data-stu-id="4702a-275">During the timeout period, hosting:</span></span>

* <span data-ttu-id="4702a-276">Dispara [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="4702a-276">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4702a-277">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="4702a-277">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="4702a-278">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="4702a-278">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4702a-279">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="4702a-279">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4702a-280">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="4702a-280">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="4702a-281">Assembly de inicialização</span><span class="sxs-lookup"><span data-stu-id="4702a-281">Startup Assembly</span></span>

<span data-ttu-id="4702a-282">Determina o assembly para pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4702a-282">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4702a-283">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="4702a-283">**Key**: startupAssembly</span></span>  
<span data-ttu-id="4702a-284">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-284">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-285">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="4702a-285">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4702a-286">**Definido usando**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="4702a-286">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="4702a-287">**Variável de ambiente**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4702a-287">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4702a-288">O assembly por nome (`string`) ou por tipo (`TStartup`) pode ser referenciado.</span><span class="sxs-lookup"><span data-stu-id="4702a-288">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="4702a-289">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="4702a-289">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="4702a-290">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="4702a-290">Web Root</span></span>

<span data-ttu-id="4702a-291">Define o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-291">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="4702a-292">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="4702a-292">**Key**: webroot</span></span>  
<span data-ttu-id="4702a-293">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="4702a-293">**Type**: *string*</span></span>  
<span data-ttu-id="4702a-294">**Padrão**: se não for especificado, o padrão será "(Raiz do conteúdo)/wwwroot", se o caminho existir.</span><span class="sxs-lookup"><span data-stu-id="4702a-294">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="4702a-295">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="4702a-295">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="4702a-296">**Definido usando**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="4702a-296">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="4702a-297">**Variável de ambiente**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4702a-297">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

## <a name="override-configuration"></a><span data-ttu-id="4702a-298">Substituir configuração</span><span class="sxs-lookup"><span data-stu-id="4702a-298">Override configuration</span></span>

<span data-ttu-id="4702a-299">Use [Configuração](xref:fundamentals/configuration/index) para configurar o host Web.</span><span class="sxs-lookup"><span data-stu-id="4702a-299">Use [Configuration](xref:fundamentals/configuration/index) to configure the web host.</span></span> <span data-ttu-id="4702a-300">No exemplo a seguir, a configuração do host é especificada, opcionalmente, em um arquivo *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4702a-300">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="4702a-301">Qualquer configuração carregada do arquivo *hostsettings.json* pode ser substituída por argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="4702a-301">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="4702a-302">A configuração de build (no `config`) é usada para configurar o host com [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="4702a-302">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="4702a-303">A configuração `IWebHostBuilder` é adicionada à configuração do aplicativo, mas o contrário não é verdade&mdash;`ConfigureAppConfiguration` não afeta a configuração `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4702a-303">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="4702a-304">Substituição da configuração fornecida por `UseUrls` pela configuração de *hostsettings.json* primeiro, e pela configuração de argumento da linha de comando depois:</span><span class="sxs-lookup"><span data-stu-id="4702a-304">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="4702a-305">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4702a-305">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="4702a-306">Atualmente, o método de extensão [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) não é capaz de analisar uma seção de configuração retornada por `GetSection` (por exemplo, `.UseConfiguration(Configuration.GetSection("section"))`).</span><span class="sxs-lookup"><span data-stu-id="4702a-306">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="4702a-307">O método `GetSection` filtra as chaves de configuração da seção solicitada, mas deixa o nome da seção nas chaves (por exemplo, `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="4702a-307">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="4702a-308">O método `UseConfiguration` espera que as chaves correspondam às chaves `WebHostBuilder` (por exemplo, `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="4702a-308">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="4702a-309">A presença do nome da seção nas chaves impede que os valores da seção configurem o host.</span><span class="sxs-lookup"><span data-stu-id="4702a-309">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="4702a-310">Esse problema será corrigido em uma próxima versão.</span><span class="sxs-lookup"><span data-stu-id="4702a-310">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="4702a-311">Para obter mais informações e soluções alternativas, consulte [Passar a seção de configuração para WebHostBuilder.UseConfiguration usa chaves completas](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="4702a-311">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="4702a-312">`UseConfiguration` somente copia as chaves do `IConfiguration` fornecido para a configuração do construtor de host.</span><span class="sxs-lookup"><span data-stu-id="4702a-312">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="4702a-313">Portanto, definir `reloadOnChange: true` para arquivos de configuração JSON, INI e XML não tem nenhum efeito.</span><span class="sxs-lookup"><span data-stu-id="4702a-313">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="4702a-314">Para especificar o host executado em uma URL específica, o valor desejado pode ser passado em um prompt de comando ao executar [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="4702a-314">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="4702a-315">O argumento de linha de comando substitui o valor `urls` do arquivo *hostsettings.json* e o servidor escuta na porta 8080:</span><span class="sxs-lookup"><span data-stu-id="4702a-315">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="4702a-316">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="4702a-316">Manage the host</span></span>

<span data-ttu-id="4702a-317">**Executar**</span><span class="sxs-lookup"><span data-stu-id="4702a-317">**Run**</span></span>

<span data-ttu-id="4702a-318">O método `Run` inicia o aplicativo Web e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="4702a-318">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="4702a-319">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="4702a-319">**Start**</span></span>

<span data-ttu-id="4702a-320">Execute o host sem bloqueio, chamando seu método `Start`:</span><span class="sxs-lookup"><span data-stu-id="4702a-320">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="4702a-321">Se uma lista de URLs for passada para o método `Start`, ele escutará nas URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="4702a-321">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="4702a-322">O aplicativo pode inicializar e iniciar um novo host usando os padrões pré-configurados de `CreateDefaultBuilder`, usando um método estático conveniente.</span><span class="sxs-lookup"><span data-stu-id="4702a-322">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="4702a-323">Esses métodos iniciam o servidor sem uma saída do console e com [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) e aguardam uma quebra (Ctrl-C/SIGINT ou SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="4702a-323">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="4702a-324">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="4702a-324">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="4702a-325">Inicie com um `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="4702a-325">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4702a-326">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="4702a-326">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="4702a-327">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="4702a-327">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4702a-328">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="4702a-328">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4702a-329">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="4702a-329">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="4702a-330">Inicie com uma URL e `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="4702a-330">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="4702a-331">Produz o mesmo resultado que **Start(RequestDelegate app)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="4702a-331">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="4702a-332">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="4702a-332">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="4702a-333">Use uma instância de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar o middleware de roteamento:</span><span class="sxs-lookup"><span data-stu-id="4702a-333">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="4702a-334">Use as seguintes solicitações de navegador com o exemplo:</span><span class="sxs-lookup"><span data-stu-id="4702a-334">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="4702a-335">Solicitação</span><span class="sxs-lookup"><span data-stu-id="4702a-335">Request</span></span>                                    | <span data-ttu-id="4702a-336">Resposta</span><span class="sxs-lookup"><span data-stu-id="4702a-336">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="4702a-337">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="4702a-337">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="4702a-338">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="4702a-338">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="4702a-339">Gera uma exceção com a cadeia de caracteres "ooops!"</span><span class="sxs-lookup"><span data-stu-id="4702a-339">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="4702a-340">Gera uma exceção com a cadeia de caracteres "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="4702a-340">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="4702a-341">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="4702a-341">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="4702a-342">Olá, Mundo!</span><span class="sxs-lookup"><span data-stu-id="4702a-342">Hello World!</span></span>                             |

<span data-ttu-id="4702a-343">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="4702a-343">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4702a-344">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="4702a-344">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4702a-345">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="4702a-345">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="4702a-346">Use uma URL e uma instância de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4702a-346">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="4702a-347">Produz o mesmo resultado que **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="4702a-347">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="4702a-348">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="4702a-348">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="4702a-349">Forneça um delegado para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4702a-349">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="4702a-350">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="4702a-350">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="4702a-351">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="4702a-351">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="4702a-352">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="4702a-352">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="4702a-353">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="4702a-353">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="4702a-354">Forneça um delegado e uma URL para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4702a-354">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="4702a-355">Produz o mesmo resultado que **StartWith(Action&lt;IApplicationBuilder&gt; app)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="4702a-355">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4702a-356">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="4702a-356">IHostingEnvironment interface</span></span>

<span data-ttu-id="4702a-357">A [interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornece informações sobre o ambiente de hospedagem na Web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-357">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="4702a-358">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="4702a-358">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="4702a-359">Uma [abordagem baseada em convenção](xref:fundamentals/environments#environment-based-startup-class-and-methods) pode ser usada para configurar o aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="4702a-359">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="4702a-360">Como alternativa, injete o `IHostingEnvironment` no construtor `Startup` para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4702a-360">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="4702a-361">Além do método de extensão `IsDevelopment`, `IHostingEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="4702a-361">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="4702a-362">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4702a-362">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4702a-363">O serviço `IHostingEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="4702a-363">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
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

<span data-ttu-id="4702a-364">`IHostingEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/index#write-middleware) personalizado:</span><span class="sxs-lookup"><span data-stu-id="4702a-364">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/index#write-middleware):</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4702a-365">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4702a-365">IApplicationLifetime interface</span></span>

<span data-ttu-id="4702a-366">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite atividades pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="4702a-366">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="4702a-367">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="4702a-367">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4702a-368">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="4702a-368">Cancellation Token</span></span>    | <span data-ttu-id="4702a-369">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="4702a-369">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="4702a-370">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="4702a-370">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="4702a-371">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="4702a-371">The host has fully started.</span></span> |
| [<span data-ttu-id="4702a-372">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="4702a-372">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="4702a-373">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4702a-373">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4702a-374">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="4702a-374">All requests should be processed.</span></span> <span data-ttu-id="4702a-375">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="4702a-375">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="4702a-376">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="4702a-376">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="4702a-377">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="4702a-377">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4702a-378">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="4702a-378">Requests may still be processing.</span></span> <span data-ttu-id="4702a-379">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="4702a-379">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="4702a-380">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4702a-380">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="4702a-381">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="4702a-381">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="4702a-382">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="4702a-382">Scope validation</span></span>

<span data-ttu-id="4702a-383">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) define [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` quando o ambiente do aplicativo é de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="4702a-383">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="4702a-384">Quando `ValidateScopes` está definido como `true`, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="4702a-384">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="4702a-385">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="4702a-385">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="4702a-386">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="4702a-386">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="4702a-387">O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado.</span><span class="sxs-lookup"><span data-stu-id="4702a-387">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="4702a-388">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="4702a-388">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="4702a-389">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="4702a-389">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="4702a-390">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="4702a-390">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="4702a-391">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="4702a-391">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="4702a-392">Para que os escopos sempre sejam validados, incluindo no ambiente de produção, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) com [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) no construtor do host:</span><span class="sxs-lookup"><span data-stu-id="4702a-392">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="4702a-393">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4702a-393">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
