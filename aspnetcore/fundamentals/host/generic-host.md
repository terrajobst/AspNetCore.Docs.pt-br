---
title: Host Genérico .NET
author: guardrex
description: Saiba mais sobre o Host Genérico no ASP.NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: bb6afe59fcad685d18cdc9c8d90cfcc7b3a6541d
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809183"
---
# <a name="net-generic-host"></a><span data-ttu-id="7767c-103">Host Genérico .NET</span><span class="sxs-lookup"><span data-stu-id="7767c-103">.NET Generic Host</span></span>

<span data-ttu-id="7767c-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7767c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7767c-105">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="7767c-105">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="7767c-106">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-106">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="7767c-107">Este artigo aborda o Host Genérico do .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="7767c-107">This article covers the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="7767c-108">O Host Genérico é diferente do host da Web, visto que separa o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="7767c-108">Generic Host differs from Web Host in that it decouples the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="7767c-109">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP podem usar o Host Genérico e se beneficiar de recursos em paralelo, tais como configuração, DI (injeção de dependência) e registro em log.</span><span class="sxs-lookup"><span data-stu-id="7767c-109">Messaging, background tasks, and other non-HTTP workloads can use Generic Host and benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="7767c-110">Da versão 3.0 do ASP.NET Core em diante, o Host Genérico é recomendável para cargas de trabalho HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="7767c-110">Starting in ASP.NET Core 3.0, Generic Host is recommended for both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="7767c-111">Uma implementação do servidor HTTP, se for incluída, será executada como uma implementação de <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="7767c-111">An HTTP server implementation, if included, runs as an implementation of <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="7767c-112"><xref:Microsoft.Extensions.Hosting.IHostedService> é uma interface que também pode ser usada para outras cargas de trabalho.</span><span class="sxs-lookup"><span data-stu-id="7767c-112"><xref:Microsoft.Extensions.Hosting.IHostedService> is an interface that can be used for other workloads as well.</span></span>

<span data-ttu-id="7767c-113">O host da Web não é mais recomendado para aplicativos Web, mas permanece disponível para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="7767c-113">Web Host is no longer recommended for web apps but remains available for backward compatibility.</span></span>

> [!NOTE]
> <span data-ttu-id="7767c-114">O restante deste artigo não foi atualizado para a versão 3.0.</span><span class="sxs-lookup"><span data-stu-id="7767c-114">This remainder of this article hasn't been updated for 3.0.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7767c-115">Aplicativos ASP.NET Core configuram e iniciam um host.</span><span class="sxs-lookup"><span data-stu-id="7767c-115">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="7767c-116">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-116">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="7767c-117">Este artigo aborda o Host Genérico do ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), que é usado para hospedar aplicativos que não processam solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="7767c-117">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="7767c-118">A finalidade do Host Genérico é separar o pipeline HTTP da API de host da Web para permitir maior gama de cenários de host.</span><span class="sxs-lookup"><span data-stu-id="7767c-118">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="7767c-119">Sistema de mensagens, tarefas em segundo plano e outras cargas de trabalho não HTTP com base no benefício do Host Genérico de recursos em paralelo, como configuração, DI (injeção de dependência) e log.</span><span class="sxs-lookup"><span data-stu-id="7767c-119">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="7767c-120">O Host Genérico é novo no ASP.NET Core 2.1 e não é adequado para cenários de hospedagem na Web.</span><span class="sxs-lookup"><span data-stu-id="7767c-120">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="7767c-121">Para cenários de hospedagem na Web, use o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="7767c-121">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="7767c-122">O Host Genérico substituirá o host da Web em uma versão futura e atuar como API do host principal em cenários HTTP e não HTTP.</span><span class="sxs-lookup"><span data-stu-id="7767c-122">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

::: moniker-end

<span data-ttu-id="7767c-123">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7767c-123">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7767c-124">Ao executar o aplicativo de exemplo no [Visual Studio Code](https://code.visualstudio.com/), use um *terminal externo ou integrado*.</span><span class="sxs-lookup"><span data-stu-id="7767c-124">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="7767c-125">Não execute o exemplo em um `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="7767c-125">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="7767c-126">Para definir o console no Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="7767c-126">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="7767c-127">Abra o arquivo *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="7767c-127">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="7767c-128">Na configuração **Inicialização do .NET Core (console)**, localize a entrada **console**.</span><span class="sxs-lookup"><span data-stu-id="7767c-128">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="7767c-129">Defina o valor como `externalTerminal` ou `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="7767c-129">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="7767c-130">Introdução</span><span class="sxs-lookup"><span data-stu-id="7767c-130">Introduction</span></span>

<span data-ttu-id="7767c-131">A biblioteca do Host Genérico está disponível no namespace <xref:Microsoft.Extensions.Hosting> e é fornecida pelo pacote [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="7767c-131">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="7767c-132">O pacote `Microsoft.Extensions.Hosting` está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="7767c-132">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="7767c-133"><xref:Microsoft.Extensions.Hosting.IHostedService> é o ponto de entrada para a execução de código.</span><span class="sxs-lookup"><span data-stu-id="7767c-133"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="7767c-134">Cada implementação do <xref:Microsoft.Extensions.Hosting.IHostedService> é executada na ordem do [registro de serviço em ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="7767c-134">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="7767c-135"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> é chamado em cada <xref:Microsoft.Extensions.Hosting.IHostedService> quando o host é iniciado e <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> é chamado na ordem inversa de registro quando o host é desligado normalmente.</span><span class="sxs-lookup"><span data-stu-id="7767c-135"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="7767c-136">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="7767c-136">Set up a host</span></span>

<span data-ttu-id="7767c-137"><xref:Microsoft.Extensions.Hosting.IHostBuilder> é o principal componente usado por aplicativos e bibliotecas para inicializar, compilar e executar o host:</span><span class="sxs-lookup"><span data-stu-id="7767c-137"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="7767c-138">Opções</span><span class="sxs-lookup"><span data-stu-id="7767c-138">Options</span></span>

<span data-ttu-id="7767c-139">Os <xref:Microsoft.Extensions.Hosting.HostOptions> configuram opções para o <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="7767c-139"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="7767c-140">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="7767c-140">Shutdown timeout</span></span>

<span data-ttu-id="7767c-141">O <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> define o tempo limite para <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="7767c-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="7767c-142">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="7767c-142">The default value is five seconds.</span></span>

<span data-ttu-id="7767c-143">A seguinte configuração de opção no `Program.Main` aumenta o tempo limite de desligamento padrão de cinco segundos para 20 segundos:</span><span class="sxs-lookup"><span data-stu-id="7767c-143">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="7767c-144">Serviços padrão</span><span class="sxs-lookup"><span data-stu-id="7767c-144">Default services</span></span>

<span data-ttu-id="7767c-145">Os seguintes serviços são registrados durante a inicialização do host:</span><span class="sxs-lookup"><span data-stu-id="7767c-145">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="7767c-146">[Ambiente](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="7767c-146">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="7767c-147">[Configuração](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="7767c-147">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="7767c-148"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="7767c-148"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="7767c-149"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="7767c-149"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="7767c-150">[Opções](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="7767c-150">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="7767c-151">[Registro em log](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="7767c-151">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="7767c-152">Configuração do host</span><span class="sxs-lookup"><span data-stu-id="7767c-152">Host configuration</span></span>

<span data-ttu-id="7767c-153">A configuração do host é criada:</span><span class="sxs-lookup"><span data-stu-id="7767c-153">Host configuration is created by:</span></span>

* <span data-ttu-id="7767c-154">Chamando métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder> para definir a [raiz do conteúdo](#content-root) e o [ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="7767c-154">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="7767c-155">Lendo a configuração de provedores de configuração no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="7767c-155">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="7767c-156">Métodos de extensão</span><span class="sxs-lookup"><span data-stu-id="7767c-156">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="7767c-157">Chave do aplicativo (nome)</span><span class="sxs-lookup"><span data-stu-id="7767c-157">Application key (name)</span></span>

<span data-ttu-id="7767c-158">A propriedade [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) é definida na configuração do host durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="7767c-158">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="7767c-159">Para definir o valor explicitamente, use o [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span><span class="sxs-lookup"><span data-stu-id="7767c-159">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="7767c-160">**Chave**: applicationName</span><span class="sxs-lookup"><span data-stu-id="7767c-160">**Key**: applicationName</span></span>  
<span data-ttu-id="7767c-161">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="7767c-161">**Type**: *string*</span></span>  
<span data-ttu-id="7767c-162">**Padrão**: o nome do assembly que contém o ponto de entrada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-162">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="7767c-163">**Definido usando**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="7767c-163">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="7767c-164">**Variável de ambiente**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="7767c-164">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="7767c-165">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="7767c-165">Content root</span></span>

<span data-ttu-id="7767c-166">Essa configuração determina onde o host começa a procurar por arquivos de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="7767c-166">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="7767c-167">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="7767c-167">**Key**: contentRoot</span></span>  
<span data-ttu-id="7767c-168">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="7767c-168">**Type**: *string*</span></span>  
<span data-ttu-id="7767c-169">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="7767c-169">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="7767c-170">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="7767c-170">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="7767c-171">**Variável de ambiente**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="7767c-171">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="7767c-172">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="7767c-172">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="7767c-173">Ambiente</span><span class="sxs-lookup"><span data-stu-id="7767c-173">Environment</span></span>

<span data-ttu-id="7767c-174">Define o [ambiente](xref:fundamentals/environments) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-174">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7767c-175">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="7767c-175">**Key**: environment</span></span>  
<span data-ttu-id="7767c-176">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="7767c-176">**Type**: *string*</span></span>  
<span data-ttu-id="7767c-177">**Padrão**: Produção</span><span class="sxs-lookup"><span data-stu-id="7767c-177">**Default**: Production</span></span>  
<span data-ttu-id="7767c-178">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="7767c-178">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="7767c-179">**Variável de ambiente**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` é [opcional e definida pelo usuário](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="7767c-179">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="7767c-180">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="7767c-180">The environment can be set to any value.</span></span> <span data-ttu-id="7767c-181">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="7767c-181">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="7767c-182">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7767c-182">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="7767c-183">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="7767c-183">ConfigureHostConfiguration</span></span>

<span data-ttu-id="7767c-184"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o host.</span><span class="sxs-lookup"><span data-stu-id="7767c-184"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="7767c-185">A configuração do host é usada para inicializar o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para uso no processo de build do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-185">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="7767c-186"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="7767c-186"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="7767c-187">O host usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="7767c-187">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="7767c-188">A configuração do host flui automaticamente para a configuração do aplicativo ([ConfigureAppConfiguration](#configureappconfiguration) e o restante do aplicativo).</span><span class="sxs-lookup"><span data-stu-id="7767c-188">Host configuration automatically flows to app configuration ([ConfigureAppConfiguration](#configureappconfiguration) and the rest of the app).</span></span>

<span data-ttu-id="7767c-189">Não há provedores incluídos por padrão.</span><span class="sxs-lookup"><span data-stu-id="7767c-189">No providers are included by default.</span></span> <span data-ttu-id="7767c-190">Você deve especificar explicitamente os provedores de configuração exigidos pelo aplicativo em <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, incluindo:</span><span class="sxs-lookup"><span data-stu-id="7767c-190">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="7767c-191">Configuração de arquivo (por exemplo, de um arquivo *hostsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="7767c-191">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="7767c-192">Configuração de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7767c-192">Environment variable configuration.</span></span>
* <span data-ttu-id="7767c-193">Configuração de argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="7767c-193">Command-line argument configuration.</span></span>
* <span data-ttu-id="7767c-194">Demais provedores de configuração necessários.</span><span class="sxs-lookup"><span data-stu-id="7767c-194">Any other required configuration providers.</span></span>

<span data-ttu-id="7767c-195">A configuração de arquivo do host é habilitada especificando o caminho base do aplicativo com `SetBasePath` seguido por uma chamada a um dos [provedores de configuração do arquivo](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7767c-195">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="7767c-196">O aplicativo de exemplo usa um arquivo JSON, *hostsettings.json*, e chamadas <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> para consumir as definições de configuração de host do arquivo.</span><span class="sxs-lookup"><span data-stu-id="7767c-196">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="7767c-197">Para adicionar uma [configuração de variável de ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) do host, chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="7767c-197">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="7767c-198">`AddEnvironmentVariables` aceita um prefixo opcional definido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="7767c-198">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="7767c-199">O aplicativo de exemplo usa um prefixo igual a `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="7767c-199">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="7767c-200">O prefixo é removido quando as variáveis de ambiente são lidas.</span><span class="sxs-lookup"><span data-stu-id="7767c-200">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="7767c-201">Quando o host do aplicativo de exemplo é configurado, o valor da variável de ambiente de `PREFIX_ENVIRONMENT` torna-se o valor de configuração de host para a chave `environment`.</span><span class="sxs-lookup"><span data-stu-id="7767c-201">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="7767c-202">Durante o desenvolvimento, ao usar o [Visual Studio](https://www.visualstudio.com/) ou executar um aplicativo com `dotnet run`, as variáveis de ambiente poderão ser definidas no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7767c-202">During development when using [Visual Studio](https://www.visualstudio.com/) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="7767c-203">No [Visual Studio Code](https://code.visualstudio.com/), as variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json* durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7767c-203">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="7767c-204">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7767c-204">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="7767c-205">[A configuração de linha de comando](xref:fundamentals/configuration/index#command-line-configuration-provider) é adicionada chamando <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="7767c-205">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="7767c-206">A configuração de linha de comando é adicionada por último para permitir que os argumentos de linha de comando substituam a configuração fornecida pelos provedores de configuração anteriores.</span><span class="sxs-lookup"><span data-stu-id="7767c-206">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="7767c-207">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7767c-207">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="7767c-208">Configuração adicional pode ser fornecida com as chaves [applicationName](#application-key-name) e [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="7767c-208">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="7767c-209">Exemplo da configuração `HostBuilder` usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="7767c-209">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="7767c-210">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="7767c-210">ConfigureAppConfiguration</span></span>

<span data-ttu-id="7767c-211">A configuração de aplicativo é criada chamando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> na implementação <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7767c-211">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="7767c-212"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> usa um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para criar um <xref:Microsoft.Extensions.Configuration.IConfiguration> para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-212"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="7767c-213"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="7767c-213"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="7767c-214">O aplicativo usa a opção que define um valor por último em uma chave determinada.</span><span class="sxs-lookup"><span data-stu-id="7767c-214">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="7767c-215">A configuração criada pelo <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> está disponível em [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) para operações subsequentes e em <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="7767c-215">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="7767c-216">A configuração do aplicativo recebe automaticamente a configuração de host fornecida por [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="7767c-216">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="7767c-217">Exemplo da configuração de aplicativo usando <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="7767c-217">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="7767c-218">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7767c-218">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="7767c-219">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="7767c-219">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="7767c-220">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="7767c-220">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="7767c-221">Para mover arquivos de configurações para o diretório de saída, especifique os arquivos de configurações como [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="7767c-221">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="7767c-222">O aplicativo de exemplo move os arquivos de configurações de aplicativo JSON e *hostsettings.json* com o seguinte item `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="7767c-222">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

## <a name="configureservices"></a><span data-ttu-id="7767c-223">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="7767c-223">ConfigureServices</span></span>

<span data-ttu-id="7767c-224"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adiciona serviços ao contêiner [injeção de dependência](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-224"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7767c-225"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="7767c-225"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="7767c-226">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="7767c-226">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="7767c-227">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="7767c-227">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="7767c-228">O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa o método de extensão `AddHostedService` para adicionar um serviço para eventos de tempo de vida, `LifetimeEventsHostedService`, e uma tarefa em segundo plano programada, `TimedHostedService`, para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7767c-228">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="7767c-229">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="7767c-229">ConfigureLogging</span></span>

<span data-ttu-id="7767c-230"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adiciona um delegado para configurar o <xref:Microsoft.Extensions.Logging.ILoggingBuilder> fornecido.</span><span class="sxs-lookup"><span data-stu-id="7767c-230"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="7767c-231"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="7767c-231"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="7767c-232">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="7767c-232">UseConsoleLifetime</span></span>

<span data-ttu-id="7767c-233"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> escuta Ctrl + C/SIGINT ou SIGTERM e chama <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para iniciar o processo de desligamento.</span><span class="sxs-lookup"><span data-stu-id="7767c-233"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="7767c-234"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> desbloqueia extensões como [RunAsync](#runasync) e [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="7767c-234"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="7767c-235"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> é previamente registrado como a implementação de tempo de vida padrão.</span><span class="sxs-lookup"><span data-stu-id="7767c-235"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="7767c-236">O último tempo de vida registrado é usado.</span><span class="sxs-lookup"><span data-stu-id="7767c-236">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="7767c-237">Configuração do contêiner</span><span class="sxs-lookup"><span data-stu-id="7767c-237">Container configuration</span></span>

<span data-ttu-id="7767c-238">Para dar suporte à conexão de outros contêineres, o host pode aceitar um <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="7767c-238">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="7767c-239">O fornecimento de um alocador não faz parte do registro de contêiner de injeção de dependência, mas, em vez disso, um host intrínseco é usado para criar o contêiner de DI concreto.</span><span class="sxs-lookup"><span data-stu-id="7767c-239">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="7767c-240">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) substitui o alocador padrão usado para criar o provedor de serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-240">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="7767c-241">A configuração de contêiner personalizado é gerenciada pelo método <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="7767c-241">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="7767c-242"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> fornece uma experiência fortemente tipada para configurar o contêiner sobre a API do host subjacente.</span><span class="sxs-lookup"><span data-stu-id="7767c-242"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="7767c-243"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> pode ser chamado várias vezes com resultados aditivos.</span><span class="sxs-lookup"><span data-stu-id="7767c-243"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="7767c-244">Crie um contêiner de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7767c-244">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="7767c-245">Forneça um alocador de contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="7767c-245">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="7767c-246">Use o alocador e configure o contêiner de serviço personalizado para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7767c-246">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="7767c-247">Extensibilidade</span><span class="sxs-lookup"><span data-stu-id="7767c-247">Extensibility</span></span>

<span data-ttu-id="7767c-248">Extensibilidade de host é executada com métodos de extensão em <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="7767c-248">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="7767c-249">O exemplo a seguir mostra como um método de extensão estende uma implementação do <xref:Microsoft.Extensions.Hosting.IHostBuilder> com o exemplo do [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), demonstrado no <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="7767c-249">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="7767c-250">Um aplicativo estabelece o método de extensão `UseHostedService` para registrar o serviço hospedado passado no `T`:</span><span class="sxs-lookup"><span data-stu-id="7767c-250">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="7767c-251">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="7767c-251">Manage the host</span></span>

<span data-ttu-id="7767c-252">A implementação <xref:Microsoft.Extensions.Hosting.IHost> é responsável por iniciar e parar as implementações <xref:Microsoft.Extensions.Hosting.IHostedService> que estão registradas no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="7767c-252">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="7767c-253">Executar</span><span class="sxs-lookup"><span data-stu-id="7767c-253">Run</span></span>

<span data-ttu-id="7767c-254"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> executa o aplicativo e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="7767c-254"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="7767c-255">RunAsync</span><span class="sxs-lookup"><span data-stu-id="7767c-255">RunAsync</span></span>

<span data-ttu-id="7767c-256"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> executa o aplicativo e retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o token de cancelamento ou o desligamento é disparado:</span><span class="sxs-lookup"><span data-stu-id="7767c-256"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="7767c-257">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="7767c-257">RunConsoleAsync</span></span>

<span data-ttu-id="7767c-258"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> habilita o suporte do console, compila e inicia o host e aguarda Ctrl + C/SIGINT ou SIGTERM desligar.</span><span class="sxs-lookup"><span data-stu-id="7767c-258"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="7767c-259">Start e StopAsync</span><span class="sxs-lookup"><span data-stu-id="7767c-259">Start and StopAsync</span></span>

<span data-ttu-id="7767c-260"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> inicia o host de forma síncrona.</span><span class="sxs-lookup"><span data-stu-id="7767c-260"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="7767c-261"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> tenta parar o host dentro do tempo limite fornecido.</span><span class="sxs-lookup"><span data-stu-id="7767c-261"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="7767c-262">StartAsync e StopAsync</span><span class="sxs-lookup"><span data-stu-id="7767c-262">StartAsync and StopAsync</span></span>

<span data-ttu-id="7767c-263"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-263"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="7767c-264"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> interrompe o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-264"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="7767c-265">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="7767c-265">WaitForShutdown</span></span>

<span data-ttu-id="7767c-266"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> é disparado por meio de <xref:Microsoft.Extensions.Hosting.IHostLifetime>, como <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (escuta Ctrl + C/SIGINT ou SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="7767c-266"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="7767c-267"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="7767c-267"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="7767c-268">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="7767c-268">WaitForShutdownAsync</span></span>

<span data-ttu-id="7767c-269"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> retorna um <xref:System.Threading.Tasks.Task> que é concluído quando o desligamento é disparado por meio do token fornecido e chama <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="7767c-269"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="7767c-270">Controle externo</span><span class="sxs-lookup"><span data-stu-id="7767c-270">External control</span></span>

<span data-ttu-id="7767c-271">O controle externo do host pode ser obtido usando os métodos que podem ser chamados externamente:</span><span class="sxs-lookup"><span data-stu-id="7767c-271">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="7767c-272"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> é chamado no início de <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, que aguarda até que ele seja concluído antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="7767c-272"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="7767c-273">Isso pode ser usado para atrasar a inicialização até que seja sinalizado por um evento externo.</span><span class="sxs-lookup"><span data-stu-id="7767c-273">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="7767c-274">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="7767c-274">IHostingEnvironment interface</span></span>

<span data-ttu-id="7767c-275"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> fornece informações sobre o ambiente de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-275"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="7767c-276">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="7767c-276">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="7767c-277">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="7767c-277">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="7767c-278">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="7767c-278">IApplicationLifetime interface</span></span>

<span data-ttu-id="7767c-279"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> permite atividades pós-inicialização e desligamento, inclusive solicitações de desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="7767c-279"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="7767c-280">Três propriedades na interface são tokens de cancelamento usados para registrar métodos <xref:System.Action> que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="7767c-280">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="7767c-281">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="7767c-281">Cancellation Token</span></span> | <span data-ttu-id="7767c-282">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="7767c-282">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="7767c-283">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="7767c-283">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="7767c-284">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="7767c-284">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="7767c-285">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="7767c-285">All requests should be processed.</span></span> <span data-ttu-id="7767c-286">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="7767c-286">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="7767c-287">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="7767c-287">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="7767c-288">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="7767c-288">Requests may still be processing.</span></span> <span data-ttu-id="7767c-289">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="7767c-289">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="7767c-290">O construtor injeta o serviço <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> em qualquer classe.</span><span class="sxs-lookup"><span data-stu-id="7767c-290">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="7767c-291">O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) usa injeção de construtor em uma classe `LifetimeEventsHostedService` (uma implementação <xref:Microsoft.Extensions.Hosting.IHostedService>) para registrar os eventos.</span><span class="sxs-lookup"><span data-stu-id="7767c-291">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="7767c-292">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="7767c-292">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="7767c-293"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7767c-293"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="7767c-294">A classe a seguir usa <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="7767c-294">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7767c-295">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7767c-295">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
