---
title: Como fazer registro em log no .NET Core e no ASP.NET Core
author: rick-anderson
description: Saiba como usar a estrutura de registro em log fornecida pelo pacote do NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 3c75fdc940701b8f4d367990b5073861467079b2
ms.sourcegitcommit: bd896935e91236e03241f75e6534ad6debcecbbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77044909"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="777aa-103">Como fazer registro em log no .NET Core e no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="777aa-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="777aa-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="777aa-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="777aa-105">O .NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="777aa-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="777aa-106">Este artigo mostra como usar a API de registro em log com provedores internos.</span><span class="sxs-lookup"><span data-stu-id="777aa-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="777aa-107">A maioria dos exemplos de código mostrados neste artigo é de aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="777aa-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="777aa-108">As partes específicas de log desses trechos de código se aplicam a qualquer aplicativo .NET Core que usa o [host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="777aa-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="777aa-109">Para obter um exemplo de como usar o host genérico em um aplicativo de console não Web, consulte o arquivo *Program.cs* do [aplicativo de exemplo de tarefas em segundo plano](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="777aa-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="777aa-110">O código de registro em log de aplicativos sem host genérico difere na maneira como os [provedores são adicionados](#add-providers) e como os [agentes são criados](#create-logs).</span><span class="sxs-lookup"><span data-stu-id="777aa-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="777aa-111">Exemplos de código não host são mostrados nessas seções do artigo.</span><span class="sxs-lookup"><span data-stu-id="777aa-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="777aa-112">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="777aa-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="777aa-113">Adicionar provedores</span><span class="sxs-lookup"><span data-stu-id="777aa-113">Add providers</span></span>

<span data-ttu-id="777aa-114">Um provedor de log exibe ou armazena logs.</span><span class="sxs-lookup"><span data-stu-id="777aa-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="777aa-115">Por exemplo, o provedor de Console exibe os logs no console, e o provedor do Azure Application Insights armazena-os no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="777aa-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="777aa-116">Os logs podem ser enviados para vários destinos por meio da adição de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="777aa-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="777aa-117">Para adicionar um provedor em um aplicativo que usa um host genérico, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="777aa-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="777aa-118">Em um aplicativo de console não host, chame o método de extensão `Add{provider name}` do provedor ao criar um `LoggerFactory`:</span><span class="sxs-lookup"><span data-stu-id="777aa-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="777aa-119">`LoggerFactory` e `AddConsole` exigem uma instrução `using` para `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="777aa-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="777aa-120">Os modelos de projeto padrão do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="777aa-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="777aa-121">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="777aa-122">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="777aa-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="777aa-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="777aa-124">[EventLog](#windows-eventlog-provider) (somente quando executado no Windows)</span><span class="sxs-lookup"><span data-stu-id="777aa-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="777aa-125">Você pode substituir os provedores padrão por aqueles de sua preferência.</span><span class="sxs-lookup"><span data-stu-id="777aa-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="777aa-126">Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.</span><span class="sxs-lookup"><span data-stu-id="777aa-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="777aa-127">Para adicionar um provedor, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="777aa-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="777aa-128">O código anterior requer referências a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="777aa-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="777aa-129">O modelo de projeto padrão chama o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:</span><span class="sxs-lookup"><span data-stu-id="777aa-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="777aa-130">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-130">Console</span></span>
* <span data-ttu-id="777aa-131">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-131">Debug</span></span>
* <span data-ttu-id="777aa-132">EventSource (a partir do ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="777aa-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="777aa-133">Se você usar `CreateDefaultBuilder`, poderá substituir os provedores padrão por aqueles que preferir.</span><span class="sxs-lookup"><span data-stu-id="777aa-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="777aa-134">Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.</span><span class="sxs-lookup"><span data-stu-id="777aa-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="777aa-135">Saiba mais sobre [provedores de log internos](#built-in-logging-providers) e [provedores de log de terceiros](#third-party-logging-providers) mais adiante no artigo.</span><span class="sxs-lookup"><span data-stu-id="777aa-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="777aa-136">Criar logs</span><span class="sxs-lookup"><span data-stu-id="777aa-136">Create logs</span></span>

<span data-ttu-id="777aa-137">Para criar logs, use um objeto <xref:Microsoft.Extensions.Logging.ILogger%601>.</span><span class="sxs-lookup"><span data-stu-id="777aa-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="777aa-138">Em um aplicativo Web ou serviço hospedado, obtenha um `ILogger` da DI (injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="777aa-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="777aa-139">Em aplicativos de console não host, use o `LoggerFactory` para criar um `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="777aa-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="777aa-140">O exemplo do ASP.NET Core a seguir cria um agente de categoria `TodoApiSample.Pages.AboutModel`.</span><span class="sxs-lookup"><span data-stu-id="777aa-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="777aa-141">A *categoria* do log é uma cadeia de caracteres associada a cada log.</span><span class="sxs-lookup"><span data-stu-id="777aa-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="777aa-142">A instância de `ILogger<T>` cria logs que têm como a categoria o nome totalmente qualificado do tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="777aa-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="777aa-143">O exemplo do aplicativo de console não host a seguir cria um agente de categoria `LoggingConsoleApp.Program`.</span><span class="sxs-lookup"><span data-stu-id="777aa-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="777aa-144">Nos exemplos de ASP.NET Core e aplicativo de console a seguir, o agente é usado para criar logs de nível `Information`.</span><span class="sxs-lookup"><span data-stu-id="777aa-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="777aa-145">O *nível* de log indica a gravidade do evento registrado.</span><span class="sxs-lookup"><span data-stu-id="777aa-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="777aa-146">[Níveis](#log-level) e [categorias](#log-category) serão explicados com mais detalhes posteriormente neste artigo.</span><span class="sxs-lookup"><span data-stu-id="777aa-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="777aa-147">Criar logs na classe Programa</span><span class="sxs-lookup"><span data-stu-id="777aa-147">Create logs in the Program class</span></span>

<span data-ttu-id="777aa-148">Para gravar logs na classe `Program` de um aplicativo ASP.NET Core, obtenha uma instância de `ILogger` por meio da DI após compilar o host:</span><span class="sxs-lookup"><span data-stu-id="777aa-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="777aa-149">Não há suporte direto para o registro em log durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="777aa-149">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="777aa-150">No entanto, um agente separado pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="777aa-150">However, a separate logger can be used.</span></span> <span data-ttu-id="777aa-151">No exemplo a seguir, um agente de log do [Serilog](https://serilog.net/) é usado para fazer logon `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="777aa-151">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="777aa-152">`AddSerilog` usa a configuração estática especificada em `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="777aa-152">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="777aa-153">Criar logs na classe Startup</span><span class="sxs-lookup"><span data-stu-id="777aa-153">Create logs in the Startup class</span></span>

<span data-ttu-id="777aa-154">Para gravar logs no método `Startup.Configure` de um aplicativo ASP.NET Core, inclua um parâmetro `ILogger` na assinatura do método:</span><span class="sxs-lookup"><span data-stu-id="777aa-154">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="777aa-155">A gravação de logs antes da conclusão da configuração do contêiner de DI no método `Startup.ConfigureServices` não é uma ação compatível:</span><span class="sxs-lookup"><span data-stu-id="777aa-155">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="777aa-156">A injeção de agente no construtor `Startup` não é uma ação compatível.</span><span class="sxs-lookup"><span data-stu-id="777aa-156">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="777aa-157">A injeção de agente na assinatura do método `Startup.ConfigureServices` não é uma ação compatível</span><span class="sxs-lookup"><span data-stu-id="777aa-157">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="777aa-158">O motivo para essa restrição é que o registro em log depende da DI e da configuração, a qual por sua vez depende da DI.</span><span class="sxs-lookup"><span data-stu-id="777aa-158">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="777aa-159">O contêiner de DI não é configurado até `ConfigureServices` ser concluído.</span><span class="sxs-lookup"><span data-stu-id="777aa-159">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="777aa-160">A injeção de construtor de um agente em `Startup` funciona em versões anteriores do ASP.NET Core porque um contêiner de DI separado é criado para o host da Web.</span><span class="sxs-lookup"><span data-stu-id="777aa-160">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="777aa-161">Para obter informações sobre por que apenas um contêiner é criado para o host genérico, consulte o [anúncio de alteração da falha](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="777aa-161">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="777aa-162">Se precisar configurar um serviço que dependa de `ILogger<T>`, você ainda poderá fazer isso usando a injeção de construtor ou fornecendo um método de fábrica.</span><span class="sxs-lookup"><span data-stu-id="777aa-162">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="777aa-163">A abordagem do método de fábrica é recomendada somente se não há nenhuma outra opção.</span><span class="sxs-lookup"><span data-stu-id="777aa-163">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="777aa-164">Por exemplo, suponha que você precise preencher uma propriedade com um serviço da DI:</span><span class="sxs-lookup"><span data-stu-id="777aa-164">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="777aa-165">O código realçado anterior é um `Func` que é executado na primeira vez que o contêiner de DI precisa construir uma instância de `MyService`.</span><span class="sxs-lookup"><span data-stu-id="777aa-165">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="777aa-166">Você pode acessar qualquer um dos serviços registrados dessa maneira.</span><span class="sxs-lookup"><span data-stu-id="777aa-166">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="777aa-167">Criar logs na inicialização</span><span class="sxs-lookup"><span data-stu-id="777aa-167">Create logs in Startup</span></span>

<span data-ttu-id="777aa-168">Para gravar logs na classe `Startup`, inclua um parâmetro `ILogger` na assinatura de construtor:</span><span class="sxs-lookup"><span data-stu-id="777aa-168">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="777aa-169">Criar logs na classe Programa</span><span class="sxs-lookup"><span data-stu-id="777aa-169">Create logs in the Program class</span></span>

<span data-ttu-id="777aa-170">Para gravar logs na classe `Program`, obtenha uma instância `ILogger` da DI:</span><span class="sxs-lookup"><span data-stu-id="777aa-170">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="777aa-171">Não há suporte direto para o registro em log durante a construção do host.</span><span class="sxs-lookup"><span data-stu-id="777aa-171">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="777aa-172">No entanto, um agente separado pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="777aa-172">However, a separate logger can be used.</span></span> <span data-ttu-id="777aa-173">No exemplo a seguir, um agente de log do [Serilog](https://serilog.net/) é usado para fazer logon `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="777aa-173">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="777aa-174">`AddSerilog` usa a configuração estática especificada em `Log.Logger`:</span><span class="sxs-lookup"><span data-stu-id="777aa-174">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="777aa-175">Sem métodos de agente assíncronos</span><span class="sxs-lookup"><span data-stu-id="777aa-175">No asynchronous logger methods</span></span>

<span data-ttu-id="777aa-176">O registro em log deve ser tão rápido que não justifique o custo de desempenho de código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="777aa-176">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="777aa-177">Se o armazenamento de dados em log estiver lento, não grave diretamente nele.</span><span class="sxs-lookup"><span data-stu-id="777aa-177">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="777aa-178">Grave as mensagens de log em um repositório rápido primeiro e, depois, mova-as para um repositório lento.</span><span class="sxs-lookup"><span data-stu-id="777aa-178">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="777aa-179">Por exemplo, se você estiver enviado logs para o SQL Server, convém não fazer isso diretamente em um método `Log`, uma vez que os métodos `Log` são síncronos.</span><span class="sxs-lookup"><span data-stu-id="777aa-179">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="777aa-180">Em vez disso, adicione mensagens de log de forma síncrona a uma fila na memória e faça com que uma função de trabalho de plano de fundo efetue pull das mensagens para fora da fila para fazer o trabalho assíncrono de envio de dados por push para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="777aa-180">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="777aa-181">Para obter mais informações, consulte [este](https://github.com/aspnet/AspNetCore.Docs/issues/11801) problema do github.</span><span class="sxs-lookup"><span data-stu-id="777aa-181">For more information, see [this](https://github.com/aspnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="777aa-182">Configuração</span><span class="sxs-lookup"><span data-stu-id="777aa-182">Configuration</span></span>

<span data-ttu-id="777aa-183">A configuração do provedor de logs é fornecida por um ou mais provedores de sincronização:</span><span class="sxs-lookup"><span data-stu-id="777aa-183">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="777aa-184">Formatos de arquivo (INI, JSON e XML).</span><span class="sxs-lookup"><span data-stu-id="777aa-184">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="777aa-185">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="777aa-185">Command-line arguments.</span></span>
* <span data-ttu-id="777aa-186">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="777aa-186">Environment variables.</span></span>
* <span data-ttu-id="777aa-187">Objetos do .NET na memória.</span><span class="sxs-lookup"><span data-stu-id="777aa-187">In-memory .NET objects.</span></span>
* <span data-ttu-id="777aa-188">O armazenamento do [Secret Manager](xref:security/app-secrets) não criptografado.</span><span class="sxs-lookup"><span data-stu-id="777aa-188">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="777aa-189">Um repositório de usuário criptografado, como o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="777aa-189">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="777aa-190">Provedores personalizados (instalados ou criados).</span><span class="sxs-lookup"><span data-stu-id="777aa-190">Custom providers (installed or created).</span></span>

<span data-ttu-id="777aa-191">Por exemplo, a configuração de log geralmente é fornecida pela seção `Logging` dos arquivos de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-191">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="777aa-192">O exemplo a seguir mostra o conteúdo de um típico arquivo *appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="777aa-192">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="777aa-193">A propriedade `Logging` pode ter `LogLevel` e propriedades do provedor de logs (o Console é mostrado).</span><span class="sxs-lookup"><span data-stu-id="777aa-193">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="777aa-194">A propriedade `LogLevel` em `Logging` especifica o [nível](#log-level) mínimo para log nas categorias selecionadas.</span><span class="sxs-lookup"><span data-stu-id="777aa-194">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="777aa-195">No exemplo, as categorias `System` e `Microsoft` têm log no nível `Information`, e todas as outras no nível `Debug`.</span><span class="sxs-lookup"><span data-stu-id="777aa-195">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="777aa-196">Outras propriedades em `Logging` especificam provedores de logs.</span><span class="sxs-lookup"><span data-stu-id="777aa-196">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="777aa-197">O exemplo se refere ao provedor de Console.</span><span class="sxs-lookup"><span data-stu-id="777aa-197">The example is for the Console provider.</span></span> <span data-ttu-id="777aa-198">Se um provedor oferecer suporte a [escopos de log](#log-scopes), `IncludeScopes` indicará se eles estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="777aa-198">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="777aa-199">Uma propriedade de provedor (como `Console`, no exemplo) também pode especificar uma propriedade `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="777aa-199">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="777aa-200">`LogLevel` em um provedor especifica os níveis de log para esse provedor.</span><span class="sxs-lookup"><span data-stu-id="777aa-200">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="777aa-201">Se os níveis forem especificados em `Logging.{providername}.LogLevel`, eles substituirão o que estiver definido em `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="777aa-201">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="777aa-202">A API de registro em log não inclui um cenário para alterar os níveis de log enquanto um aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="777aa-202">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="777aa-203">No entanto, alguns provedores de configuração são capazes de recarregar a configuração, o que exige um efeito imediato na configuração de log.</span><span class="sxs-lookup"><span data-stu-id="777aa-203">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="777aa-204">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider), que é adicionado pelo `CreateDefaultBuilder` para ler arquivos de configuração, recarrega a configuração de log por padrão.</span><span class="sxs-lookup"><span data-stu-id="777aa-204">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="777aa-205">Se a configuração for alterada no código enquanto um aplicativo estiver em execução, o aplicativo poderá chamar [IConfigurationRoot. recarregar](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) para atualizar a configuração de log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-205">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="777aa-206">Saiba mais sobre como implementar provedores de configuração em <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="777aa-206">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="777aa-207">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="777aa-207">Sample logging output</span></span>

<span data-ttu-id="777aa-208">Com o código de exemplo mostrado na seção anterior, os logs serão exibidos no console quando o aplicativo for executado pela linha de comando.</span><span class="sxs-lookup"><span data-stu-id="777aa-208">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="777aa-209">Aqui está um exemplo da saída do console:</span><span class="sxs-lookup"><span data-stu-id="777aa-209">Here's an example of console output:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

<span data-ttu-id="777aa-210">Os logs anteriores foram gerados por meio de uma solicitação HTTP Get para o aplicativo de exemplo em `http://localhost:5000/api/todo/0`.</span><span class="sxs-lookup"><span data-stu-id="777aa-210">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="777aa-211">Veja um exemplo de como os mesmos logs aparecem na janela Depuração quando você executa o aplicativo de exemplo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="777aa-211">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="777aa-212">Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApiSample".</span><span class="sxs-lookup"><span data-stu-id="777aa-212">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="777aa-213">Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="777aa-213">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="777aa-214">O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.</span><span class="sxs-lookup"><span data-stu-id="777aa-214">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="777aa-215">Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApi".</span><span class="sxs-lookup"><span data-stu-id="777aa-215">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="777aa-216">Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="777aa-216">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="777aa-217">O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.</span><span class="sxs-lookup"><span data-stu-id="777aa-217">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="777aa-218">O restante deste artigo explica alguns detalhes e opções para registro em log.</span><span class="sxs-lookup"><span data-stu-id="777aa-218">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="777aa-219">Pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="777aa-219">NuGet packages</span></span>

<span data-ttu-id="777aa-220">As interfaces `ILogger` e `ILoggerFactory` estão em [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e as implementações padrão para elas estão em [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span><span class="sxs-lookup"><span data-stu-id="777aa-220">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="777aa-221">Categoria do log</span><span class="sxs-lookup"><span data-stu-id="777aa-221">Log category</span></span>

<span data-ttu-id="777aa-222">Quando um objeto `ILogger` é criado, uma *categoria* é especificada para ele.</span><span class="sxs-lookup"><span data-stu-id="777aa-222">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="777aa-223">Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="777aa-223">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="777aa-224">A categoria pode ser qualquer cadeia de caracteres, mas a convenção é usar o nome da classe, como "TodoApi.Controllers.TodoController".</span><span class="sxs-lookup"><span data-stu-id="777aa-224">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="777aa-225">Use `ILogger<T>` para obter uma instância `ILogger` que usa o nome de tipo totalmente qualificado do `T` como a categoria:</span><span class="sxs-lookup"><span data-stu-id="777aa-225">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="777aa-226">Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:</span><span class="sxs-lookup"><span data-stu-id="777aa-226">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="777aa-227">`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.</span><span class="sxs-lookup"><span data-stu-id="777aa-227">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="777aa-228">Nível de log</span><span class="sxs-lookup"><span data-stu-id="777aa-228">Log level</span></span>

<span data-ttu-id="777aa-229">Todo log especifica um valor <xref:Microsoft.Extensions.Logging.LogLevel>.</span><span class="sxs-lookup"><span data-stu-id="777aa-229">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="777aa-230">O nível de log indica a gravidade ou importância.</span><span class="sxs-lookup"><span data-stu-id="777aa-230">The log level indicates the severity or importance.</span></span> <span data-ttu-id="777aa-231">Por exemplo, você pode gravar um log `Information` quando um método é finalizado normalmente e um log `Warning` quando um método retorna um código de status *404 Não Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="777aa-231">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="777aa-232">O código a seguir cria os logs `Information` e `Warning`:</span><span class="sxs-lookup"><span data-stu-id="777aa-232">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="777aa-233">No código anterior, o primeiro parâmetro é a [ID de evento de log](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="777aa-233">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="777aa-234">O segundo parâmetro é um modelo de mensagem com espaços reservados para valores de argumento fornecidos pelos parâmetros de método restantes.</span><span class="sxs-lookup"><span data-stu-id="777aa-234">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="777aa-235">Os parâmetros de método serão explicados com posteriormente neste artigo, na [seção de modelos de mensagem](#log-message-template).</span><span class="sxs-lookup"><span data-stu-id="777aa-235">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="777aa-236">Os métodos de log que incluem o nível no nome do método (por exemplo, `LogInformation` e `LogWarning`) são [métodos de extensão para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="777aa-236">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="777aa-237">Esses métodos chamam um método `Log` que recebe um parâmetro `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="777aa-237">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="777aa-238">Você pode chamar o método `Log` diretamente em vez de um desses métodos de extensão, mas a sintaxe é relativamente complicada.</span><span class="sxs-lookup"><span data-stu-id="777aa-238">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="777aa-239">Para saber mais, veja <xref:Microsoft.Extensions.Logging.ILogger> e o [código-fonte de extensões de agente](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="777aa-239">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="777aa-240">O ASP.NET Core define os seguintes níveis de log, ordenados aqui da menor para a maior gravidade.</span><span class="sxs-lookup"><span data-stu-id="777aa-240">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="777aa-241">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="777aa-241">Trace = 0</span></span>

  <span data-ttu-id="777aa-242">Para obter informações que normalmente são valiosas somente para depuração.</span><span class="sxs-lookup"><span data-stu-id="777aa-242">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="777aa-243">Essas mensagens podem conter dados confidenciais de aplicativos e, portanto, não devem ser habilitadas em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="777aa-243">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="777aa-244">*Desabilitado por padrão.*</span><span class="sxs-lookup"><span data-stu-id="777aa-244">*Disabled by default.*</span></span>

* <span data-ttu-id="777aa-245">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="777aa-245">Debug = 1</span></span>

  <span data-ttu-id="777aa-246">Para obter informações que possam ser úteis durante o desenvolvimento e a depuração.</span><span class="sxs-lookup"><span data-stu-id="777aa-246">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="777aa-247">Exemplo: `Entering method Configure with flag set to true.` habilite logs de nível `Debug` em produção somente ao solucionar problemas, devido ao alto volume de logs.</span><span class="sxs-lookup"><span data-stu-id="777aa-247">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="777aa-248">Information = 2</span><span class="sxs-lookup"><span data-stu-id="777aa-248">Information = 2</span></span>

  <span data-ttu-id="777aa-249">Para rastrear o fluxo geral do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-249">For tracking the general flow of the app.</span></span> <span data-ttu-id="777aa-250">Esses logs normalmente têm algum valor a longo prazo.</span><span class="sxs-lookup"><span data-stu-id="777aa-250">These logs typically have some long-term value.</span></span> <span data-ttu-id="777aa-251">Exemplo: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="777aa-251">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="777aa-252">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="777aa-252">Warning = 3</span></span>

  <span data-ttu-id="777aa-253">Para eventos anormais ou inesperados no fluxo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-253">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="777aa-254">Eles podem incluir erros ou outras condições que não fazem com que o aplicativo pare, mas que talvez precisem ser investigados.</span><span class="sxs-lookup"><span data-stu-id="777aa-254">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="777aa-255">Exceções manipuladas são um local comum para usar o nível de log `Warning`.</span><span class="sxs-lookup"><span data-stu-id="777aa-255">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="777aa-256">Exemplo: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="777aa-256">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="777aa-257">Erro = 4</span><span class="sxs-lookup"><span data-stu-id="777aa-257">Error = 4</span></span>

  <span data-ttu-id="777aa-258">Para erros e exceções que não podem ser manipulados.</span><span class="sxs-lookup"><span data-stu-id="777aa-258">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="777aa-259">Essas mensagens indicam uma falha na atividade ou na operação atual (como a solicitação HTTP atual) e não uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-259">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="777aa-260">Mensagem de log de exemplo:`Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="777aa-260">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="777aa-261">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="777aa-261">Critical = 5</span></span>

  <span data-ttu-id="777aa-262">Para falhas que exigem atenção imediata.</span><span class="sxs-lookup"><span data-stu-id="777aa-262">For failures that require immediate attention.</span></span> <span data-ttu-id="777aa-263">Exemplos: cenários de perda de dados, espaço em disco insuficiente.</span><span class="sxs-lookup"><span data-stu-id="777aa-263">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="777aa-264">Use o nível de log para controlar a quantidade de saída de log que é gravada em uma mídia de armazenamento específica ou em uma janela de exibição.</span><span class="sxs-lookup"><span data-stu-id="777aa-264">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="777aa-265">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="777aa-265">For example:</span></span>

* <span data-ttu-id="777aa-266">Em produção:</span><span class="sxs-lookup"><span data-stu-id="777aa-266">In production:</span></span>
  * <span data-ttu-id="777aa-267">O registro em log na `Trace` por meio de níveis de `Information` produz um alto volume de mensagens de log detalhadas.</span><span class="sxs-lookup"><span data-stu-id="777aa-267">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="777aa-268">Para controlar os custos e não exceder os limites de armazenamento de dados, `Trace` de log por meio de mensagens de nível de `Information` para um armazenamento de dados de alto volume e baixo custo.</span><span class="sxs-lookup"><span data-stu-id="777aa-268">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="777aa-269">O registro em log em `Warning` por meio de níveis de `Critical` produz menos mensagens de log menores.</span><span class="sxs-lookup"><span data-stu-id="777aa-269">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="777aa-270">Portanto, os custos e os limites de armazenamento geralmente não são uma preocupação, o que resulta em maior flexibilidade de escolha de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="777aa-270">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="777aa-271">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="777aa-271">During development:</span></span>
  * <span data-ttu-id="777aa-272">`Warning` de log por meio de mensagens de `Critical` para o console.</span><span class="sxs-lookup"><span data-stu-id="777aa-272">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="777aa-273">Adicione `Trace` por meio de mensagens `Information` ao solucionar o problema.</span><span class="sxs-lookup"><span data-stu-id="777aa-273">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="777aa-274">A seção [Filtragem de log](#log-filtering) mais adiante neste artigo explicará como controlar os níveis de log que um provedor manipula.</span><span class="sxs-lookup"><span data-stu-id="777aa-274">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="777aa-275">O ASP.NET Core grava logs para eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="777aa-275">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="777aa-276">Os exemplos de log anteriores neste artigo excluíram logs abaixo do nível `Information`, portanto, logs de nível `Debug` ou `Trace` não foram criados.</span><span class="sxs-lookup"><span data-stu-id="777aa-276">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="777aa-277">Veja um exemplo de logs de console produzidos por meio da execução do aplicativo de exemplo configurado para mostrar logs `Debug`:</span><span class="sxs-lookup"><span data-stu-id="777aa-277">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a><span data-ttu-id="777aa-278">ID de evento de log</span><span class="sxs-lookup"><span data-stu-id="777aa-278">Log event ID</span></span>

<span data-ttu-id="777aa-279">Cada log pode especificar uma *ID do evento*.</span><span class="sxs-lookup"><span data-stu-id="777aa-279">Each log can specify an *event ID*.</span></span> <span data-ttu-id="777aa-280">O aplicativo de exemplo faz isso usando uma classe `LoggingEvents` definida localmente:</span><span class="sxs-lookup"><span data-stu-id="777aa-280">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="777aa-281">Uma ID de evento associa um conjunto de eventos.</span><span class="sxs-lookup"><span data-stu-id="777aa-281">An event ID associates a set of events.</span></span> <span data-ttu-id="777aa-282">Por exemplo, todos os logs relacionados à exibição de uma lista de itens em uma página podem ser 1001.</span><span class="sxs-lookup"><span data-stu-id="777aa-282">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="777aa-283">O provedor de logs pode armazenar a ID do evento em um campo de ID na mensagem de log ou não armazenar.</span><span class="sxs-lookup"><span data-stu-id="777aa-283">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="777aa-284">O provedor de Depuração não mostra IDs de eventos.</span><span class="sxs-lookup"><span data-stu-id="777aa-284">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="777aa-285">O provedor de console mostra IDs de evento entre colchetes após a categoria:</span><span class="sxs-lookup"><span data-stu-id="777aa-285">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="777aa-286">Modelo de mensagem de log</span><span class="sxs-lookup"><span data-stu-id="777aa-286">Log message template</span></span>

<span data-ttu-id="777aa-287">Cada log especifica um modelo de mensagem.</span><span class="sxs-lookup"><span data-stu-id="777aa-287">Each log specifies a message template.</span></span> <span data-ttu-id="777aa-288">O modelo de mensagem pode conter espaços reservados para os quais são fornecidos argumentos.</span><span class="sxs-lookup"><span data-stu-id="777aa-288">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="777aa-289">Use nomes para os espaços reservados, não números.</span><span class="sxs-lookup"><span data-stu-id="777aa-289">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="777aa-290">A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores.</span><span class="sxs-lookup"><span data-stu-id="777aa-290">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="777aa-291">No código a seguir, observe que os nomes de parâmetro estão fora de sequência no modelo de mensagem:</span><span class="sxs-lookup"><span data-stu-id="777aa-291">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="777aa-292">Esse código cria uma mensagem de log com os valores de parâmetro na sequência:</span><span class="sxs-lookup"><span data-stu-id="777aa-292">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="777aa-293">A estrutura de registros funciona dessa maneira para que os provedores de logs possam implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="777aa-293">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="777aa-294">Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado.</span><span class="sxs-lookup"><span data-stu-id="777aa-294">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="777aa-295">Essas informações permitem que os provedores de log armazenem os valores de parâmetro como campos.</span><span class="sxs-lookup"><span data-stu-id="777aa-295">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="777aa-296">Por exemplo, suponha que as chamadas de método do agente sejam assim:</span><span class="sxs-lookup"><span data-stu-id="777aa-296">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="777aa-297">Se você estiver enviando os logs para o Armazenamento de Tabelas do Azure, cada entidade da Tabela do Azure poderá ter propriedades `ID` e `RequestTime`, o que simplificará as consultas nos dados de log.</span><span class="sxs-lookup"><span data-stu-id="777aa-297">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="777aa-298">Uma consulta pode encontrar todos os logs em determinado intervalo de `RequestTime` sem analisar o tempo limite da mensagem de texto.</span><span class="sxs-lookup"><span data-stu-id="777aa-298">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="777aa-299">Exceções de registro em log</span><span class="sxs-lookup"><span data-stu-id="777aa-299">Logging exceptions</span></span>

<span data-ttu-id="777aa-300">Os métodos de agente têm sobrecargas que permitem que você passe uma exceção, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="777aa-300">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="777aa-301">Provedores diferentes manipulam as informações de exceção de maneiras diferentes.</span><span class="sxs-lookup"><span data-stu-id="777aa-301">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="777aa-302">Aqui está um exemplo da saída do provedor Depuração do código mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="777aa-302">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="777aa-303">Filtragem de linha</span><span class="sxs-lookup"><span data-stu-id="777aa-303">Log filtering</span></span>

<span data-ttu-id="777aa-304">Você pode especificar um nível de log mínimo para um provedor e uma categoria específicos ou para todos os provedores ou todas as categorias.</span><span class="sxs-lookup"><span data-stu-id="777aa-304">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="777aa-305">Os logs abaixo do nível mínimo não serão passados para esse provedor, para que não sejam exibidos ou armazenados.</span><span class="sxs-lookup"><span data-stu-id="777aa-305">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="777aa-306">Para suprimir todos os logs, especifique `LogLevel.None` como o nível de log mínimo.</span><span class="sxs-lookup"><span data-stu-id="777aa-306">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="777aa-307">O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="777aa-307">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="777aa-308">Criar regras de filtro na configuração</span><span class="sxs-lookup"><span data-stu-id="777aa-308">Create filter rules in configuration</span></span>

<span data-ttu-id="777aa-309">O código de modelo de projeto chama `CreateDefaultBuilder` para configurar o registro em log para os provedores console, Debug e EventSource (ASP.NET Core 2,2 ou posteriores).</span><span class="sxs-lookup"><span data-stu-id="777aa-309">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="777aa-310">O método `CreateDefaultBuilder` configura o registro em log para procurar a configuração em uma seção `Logging`, conforme explicado [anteriormente neste artigo](#configuration).</span><span class="sxs-lookup"><span data-stu-id="777aa-310">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="777aa-311">Os dados de configuração especificam níveis de log mínimo por provedor e por categoria, como no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="777aa-311">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="777aa-312">Este JSON cria seis regras de filtro, uma para o provedor Depuração, quatro para o provedor Console e uma para todos os provedores.</span><span class="sxs-lookup"><span data-stu-id="777aa-312">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="777aa-313">Apenas uma regra é escolhida para cada provedor quando um objeto `ILogger` é criado.</span><span class="sxs-lookup"><span data-stu-id="777aa-313">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="777aa-314">Regras de filtro no código</span><span class="sxs-lookup"><span data-stu-id="777aa-314">Filter rules in code</span></span>

<span data-ttu-id="777aa-315">O exemplo a seguir mostra como registrar regras de filtro no código:</span><span class="sxs-lookup"><span data-stu-id="777aa-315">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="777aa-316">O segundo `AddFilter` especifica o provedor Depuração usando seu nome de tipo.</span><span class="sxs-lookup"><span data-stu-id="777aa-316">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="777aa-317">O primeiro `AddFilter` se aplica a todos os provedores porque ele não especifica um tipo de provedor.</span><span class="sxs-lookup"><span data-stu-id="777aa-317">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="777aa-318">Como as regras de filtragem são aplicadas</span><span class="sxs-lookup"><span data-stu-id="777aa-318">How filtering rules are applied</span></span>

<span data-ttu-id="777aa-319">Os dados de configuração e o código `AddFilter`, mostrados nos exemplos anteriores, criam as regras mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="777aa-319">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="777aa-320">As primeiras seis vêm do exemplo de configuração e as últimas duas vêm do exemplo de código.</span><span class="sxs-lookup"><span data-stu-id="777aa-320">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="777aa-321">Número</span><span class="sxs-lookup"><span data-stu-id="777aa-321">Number</span></span> | <span data-ttu-id="777aa-322">Provedor</span><span class="sxs-lookup"><span data-stu-id="777aa-322">Provider</span></span>      | <span data-ttu-id="777aa-323">Categorias que começam com...</span><span class="sxs-lookup"><span data-stu-id="777aa-323">Categories that begin with ...</span></span>          | <span data-ttu-id="777aa-324">Nível de log mínimo</span><span class="sxs-lookup"><span data-stu-id="777aa-324">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="777aa-325">1</span><span class="sxs-lookup"><span data-stu-id="777aa-325">1</span></span>      | <span data-ttu-id="777aa-326">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-326">Debug</span></span>         | <span data-ttu-id="777aa-327">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="777aa-327">All categories</span></span>                          | <span data-ttu-id="777aa-328">Informações</span><span class="sxs-lookup"><span data-stu-id="777aa-328">Information</span></span>       |
| <span data-ttu-id="777aa-329">2</span><span class="sxs-lookup"><span data-stu-id="777aa-329">2</span></span>      | <span data-ttu-id="777aa-330">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-330">Console</span></span>       | <span data-ttu-id="777aa-331">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="777aa-331">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="777aa-332">Aviso</span><span class="sxs-lookup"><span data-stu-id="777aa-332">Warning</span></span>           |
| <span data-ttu-id="777aa-333">3</span><span class="sxs-lookup"><span data-stu-id="777aa-333">3</span></span>      | <span data-ttu-id="777aa-334">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-334">Console</span></span>       | <span data-ttu-id="777aa-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="777aa-335">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="777aa-336">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-336">Debug</span></span>             |
| <span data-ttu-id="777aa-337">4</span><span class="sxs-lookup"><span data-stu-id="777aa-337">4</span></span>      | <span data-ttu-id="777aa-338">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-338">Console</span></span>       | <span data-ttu-id="777aa-339">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="777aa-339">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="777aa-340">Erro</span><span class="sxs-lookup"><span data-stu-id="777aa-340">Error</span></span>             |
| <span data-ttu-id="777aa-341">5</span><span class="sxs-lookup"><span data-stu-id="777aa-341">5</span></span>      | <span data-ttu-id="777aa-342">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-342">Console</span></span>       | <span data-ttu-id="777aa-343">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="777aa-343">All categories</span></span>                          | <span data-ttu-id="777aa-344">Informações</span><span class="sxs-lookup"><span data-stu-id="777aa-344">Information</span></span>       |
| <span data-ttu-id="777aa-345">6</span><span class="sxs-lookup"><span data-stu-id="777aa-345">6</span></span>      | <span data-ttu-id="777aa-346">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="777aa-346">All providers</span></span> | <span data-ttu-id="777aa-347">Todas as categorias</span><span class="sxs-lookup"><span data-stu-id="777aa-347">All categories</span></span>                          | <span data-ttu-id="777aa-348">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-348">Debug</span></span>             |
| <span data-ttu-id="777aa-349">7</span><span class="sxs-lookup"><span data-stu-id="777aa-349">7</span></span>      | <span data-ttu-id="777aa-350">Todos os provedores</span><span class="sxs-lookup"><span data-stu-id="777aa-350">All providers</span></span> | <span data-ttu-id="777aa-351">Sistema</span><span class="sxs-lookup"><span data-stu-id="777aa-351">System</span></span>                                  | <span data-ttu-id="777aa-352">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-352">Debug</span></span>             |
| <span data-ttu-id="777aa-353">8</span><span class="sxs-lookup"><span data-stu-id="777aa-353">8</span></span>      | <span data-ttu-id="777aa-354">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-354">Debug</span></span>         | <span data-ttu-id="777aa-355">Microsoft</span><span class="sxs-lookup"><span data-stu-id="777aa-355">Microsoft</span></span>                               | <span data-ttu-id="777aa-356">Trace</span><span class="sxs-lookup"><span data-stu-id="777aa-356">Trace</span></span>             |

<span data-ttu-id="777aa-357">Quando um objeto `ILogger` é criado, o objeto `ILoggerFactory` seleciona uma única regra por provedor para aplicar a esse agente.</span><span class="sxs-lookup"><span data-stu-id="777aa-357">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="777aa-358">Todas as mensagens gravadas pela instância `ILogger` são filtradas com base nas regras selecionadas.</span><span class="sxs-lookup"><span data-stu-id="777aa-358">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="777aa-359">A regra mais específica possível para cada par de categoria e provedor é selecionada dentre as regras disponíveis.</span><span class="sxs-lookup"><span data-stu-id="777aa-359">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="777aa-360">O algoritmo a seguir é usado para cada provedor quando um `ILogger` é criado para uma determinada categoria:</span><span class="sxs-lookup"><span data-stu-id="777aa-360">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="777aa-361">Selecione todas as regras que correspondem ao provedor ou seu alias.</span><span class="sxs-lookup"><span data-stu-id="777aa-361">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="777aa-362">Se nenhuma correspondência for encontrada, selecione todas as regras com um provedor vazio.</span><span class="sxs-lookup"><span data-stu-id="777aa-362">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="777aa-363">Do resultado da etapa anterior, selecione as regras com o prefixo de categoria de maior correspondência.</span><span class="sxs-lookup"><span data-stu-id="777aa-363">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="777aa-364">Se nenhuma correspondência for encontrada, selecione todas as regras que não especificam uma categoria.</span><span class="sxs-lookup"><span data-stu-id="777aa-364">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="777aa-365">Se várias regras forem selecionadas, use a **última**.</span><span class="sxs-lookup"><span data-stu-id="777aa-365">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="777aa-366">Se nenhuma regra for selecionada, use `MinimumLevel`.</span><span class="sxs-lookup"><span data-stu-id="777aa-366">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="777aa-367">Com a lista anterior de regras, suponha que você crie um objeto `ILogger` para a categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span><span class="sxs-lookup"><span data-stu-id="777aa-367">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="777aa-368">Para o provedor Depuração as regras 1, 6 e 8 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="777aa-368">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="777aa-369">A regra 8 é mais específica, portanto é a que será selecionada.</span><span class="sxs-lookup"><span data-stu-id="777aa-369">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="777aa-370">Para o provedor Console as regras 3, 4, 5 e 6 se aplicam.</span><span class="sxs-lookup"><span data-stu-id="777aa-370">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="777aa-371">A regra 3 é a mais específica.</span><span class="sxs-lookup"><span data-stu-id="777aa-371">Rule 3 is most specific.</span></span>

<span data-ttu-id="777aa-372">A instância `ILogger` resultante envia logs de nível `Trace` e superior para o provedor Depuração.</span><span class="sxs-lookup"><span data-stu-id="777aa-372">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="777aa-373">Logs de nível `Debug` e superior são enviados para o provedor Console.</span><span class="sxs-lookup"><span data-stu-id="777aa-373">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="777aa-374">Aliases de provedor</span><span class="sxs-lookup"><span data-stu-id="777aa-374">Provider aliases</span></span>

<span data-ttu-id="777aa-375">Cada provedor define um *alias* que pode ser usado na configuração no lugar do nome de tipo totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="777aa-375">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="777aa-376">Para os provedores internos, use os seguintes aliases:</span><span class="sxs-lookup"><span data-stu-id="777aa-376">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="777aa-377">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-377">Console</span></span>
* <span data-ttu-id="777aa-378">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-378">Debug</span></span>
* <span data-ttu-id="777aa-379">EventSource</span><span class="sxs-lookup"><span data-stu-id="777aa-379">EventSource</span></span>
* <span data-ttu-id="777aa-380">EventLog</span><span class="sxs-lookup"><span data-stu-id="777aa-380">EventLog</span></span>
* <span data-ttu-id="777aa-381">TraceSource</span><span class="sxs-lookup"><span data-stu-id="777aa-381">TraceSource</span></span>
* <span data-ttu-id="777aa-382">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="777aa-382">AzureAppServicesFile</span></span>
* <span data-ttu-id="777aa-383">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="777aa-383">AzureAppServicesBlob</span></span>
* <span data-ttu-id="777aa-384">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="777aa-384">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="777aa-385">Nível mínimo padrão</span><span class="sxs-lookup"><span data-stu-id="777aa-385">Default minimum level</span></span>

<span data-ttu-id="777aa-386">Há uma configuração de nível mínimo que entra em vigor somente se nenhuma regra de código ou de configuração se aplicar a um provedor e uma categoria determinados.</span><span class="sxs-lookup"><span data-stu-id="777aa-386">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="777aa-387">O exemplo a seguir mostra como definir o nível mínimo:</span><span class="sxs-lookup"><span data-stu-id="777aa-387">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="777aa-388">Se você não definir explicitamente o nível mínimo, o valor padrão será `Information`, o que significa que logs `Trace` e `Debug` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="777aa-388">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="777aa-389">Funções de filtro</span><span class="sxs-lookup"><span data-stu-id="777aa-389">Filter functions</span></span>

<span data-ttu-id="777aa-390">Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código.</span><span class="sxs-lookup"><span data-stu-id="777aa-390">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="777aa-391">O código na função tem acesso ao tipo de provedor, à categoria e ao nível de log.</span><span class="sxs-lookup"><span data-stu-id="777aa-391">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="777aa-392">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="777aa-392">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="777aa-393">Categorias e níveis de sistema</span><span class="sxs-lookup"><span data-stu-id="777aa-393">System categories and levels</span></span>

<span data-ttu-id="777aa-394">Veja algumas categorias usadas pelo ASP.NET Core e Entity Framework Core, com anotações sobre quais logs esperar delas:</span><span class="sxs-lookup"><span data-stu-id="777aa-394">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="777aa-395">Categoria</span><span class="sxs-lookup"><span data-stu-id="777aa-395">Category</span></span>                            | <span data-ttu-id="777aa-396">Observações</span><span class="sxs-lookup"><span data-stu-id="777aa-396">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="777aa-397">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="777aa-397">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="777aa-398">Diagnóstico geral de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="777aa-398">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="777aa-399">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="777aa-399">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="777aa-400">Quais chaves foram consideradas, encontradas e usadas.</span><span class="sxs-lookup"><span data-stu-id="777aa-400">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="777aa-401">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="777aa-401">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="777aa-402">Hosts permitidos.</span><span class="sxs-lookup"><span data-stu-id="777aa-402">Hosts allowed.</span></span> |
| <span data-ttu-id="777aa-403">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="777aa-403">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="777aa-404">Quanto tempo levou para que as solicitações de HTTP fossem concluídas e em que horário foram iniciadas.</span><span class="sxs-lookup"><span data-stu-id="777aa-404">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="777aa-405">Quais assemblies de inicialização de hospedagem foram carregados.</span><span class="sxs-lookup"><span data-stu-id="777aa-405">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="777aa-406">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="777aa-406">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="777aa-407">Diagnóstico do MVC e Razor.</span><span class="sxs-lookup"><span data-stu-id="777aa-407">MVC and Razor diagnostics.</span></span> <span data-ttu-id="777aa-408">Model binding, execução de filtro, compilação de exibição, seleção de ação.</span><span class="sxs-lookup"><span data-stu-id="777aa-408">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="777aa-409">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="777aa-409">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="777aa-410">Informações de correspondência de rotas.</span><span class="sxs-lookup"><span data-stu-id="777aa-410">Route matching information.</span></span> |
| <span data-ttu-id="777aa-411">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="777aa-411">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="777aa-412">Respostas de início, parada e atividade da conexão.</span><span class="sxs-lookup"><span data-stu-id="777aa-412">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="777aa-413">Informações sobre o certificado HTTPS.</span><span class="sxs-lookup"><span data-stu-id="777aa-413">HTTPS certificate information.</span></span> |
| <span data-ttu-id="777aa-414">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="777aa-414">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="777aa-415">Arquivos atendidos.</span><span class="sxs-lookup"><span data-stu-id="777aa-415">Files served.</span></span> |
| <span data-ttu-id="777aa-416">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="777aa-416">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="777aa-417">Diagnóstico geral do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="777aa-417">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="777aa-418">Atividade e configuração do banco de dados, detecção de alterações, migrações.</span><span class="sxs-lookup"><span data-stu-id="777aa-418">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="777aa-419">Escopos de log</span><span class="sxs-lookup"><span data-stu-id="777aa-419">Log scopes</span></span>

 <span data-ttu-id="777aa-420">Um *escopo* pode agrupar um conjunto de operações lógicas.</span><span class="sxs-lookup"><span data-stu-id="777aa-420">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="777aa-421">Esse agrupamento pode ser usado para anexar os mesmos dados para cada log criado como parte de um conjunto.</span><span class="sxs-lookup"><span data-stu-id="777aa-421">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="777aa-422">Por exemplo, todo log criado como parte do processamento de uma transação pode incluir a ID da transação.</span><span class="sxs-lookup"><span data-stu-id="777aa-422">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="777aa-423">Um escopo é um tipo `IDisposable` retornado pelo método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e que dura até que seja descartado.</span><span class="sxs-lookup"><span data-stu-id="777aa-423">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="777aa-424">Use um escopo por meio do encapsulamento de chamadas de agente em um bloco `using`:</span><span class="sxs-lookup"><span data-stu-id="777aa-424">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="777aa-425">O código a seguir habilita os escopos para o provedor de console:</span><span class="sxs-lookup"><span data-stu-id="777aa-425">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="777aa-426">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="777aa-426">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="777aa-427">A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.</span><span class="sxs-lookup"><span data-stu-id="777aa-427">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="777aa-428">Saiba mais sobre como configurar na seção [Configuração](#configuration).</span><span class="sxs-lookup"><span data-stu-id="777aa-428">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="777aa-429">Cada mensagem de log inclui as informações com escopo definido:</span><span class="sxs-lookup"><span data-stu-id="777aa-429">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="777aa-430">Provedores de log internos</span><span class="sxs-lookup"><span data-stu-id="777aa-430">Built-in logging providers</span></span>

<span data-ttu-id="777aa-431">O ASP.NET Core vem com os seguintes provedores:</span><span class="sxs-lookup"><span data-stu-id="777aa-431">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="777aa-432">Console</span><span class="sxs-lookup"><span data-stu-id="777aa-432">Console</span></span>](#console-provider)
* [<span data-ttu-id="777aa-433">Depurar</span><span class="sxs-lookup"><span data-stu-id="777aa-433">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="777aa-434">EventSource</span><span class="sxs-lookup"><span data-stu-id="777aa-434">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="777aa-435">EventLog</span><span class="sxs-lookup"><span data-stu-id="777aa-435">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="777aa-436">TraceSource</span><span class="sxs-lookup"><span data-stu-id="777aa-436">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="777aa-437">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="777aa-437">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="777aa-438">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="777aa-438">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="777aa-439">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="777aa-439">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="777aa-440">Para obter informações sobre StdOut e o log de depuração com o Módulo do ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="777aa-440">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="777aa-441">Provedor do console</span><span class="sxs-lookup"><span data-stu-id="777aa-441">Console provider</span></span>

<span data-ttu-id="777aa-442">O pacote de provedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envia a saída de log para o console.</span><span class="sxs-lookup"><span data-stu-id="777aa-442">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="777aa-443">Para ver a saída de registro em log de console, abra um prompt de comando na pasta do projeto e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="777aa-443">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="777aa-444">Depurar provedor</span><span class="sxs-lookup"><span data-stu-id="777aa-444">Debug provider</span></span>

<span data-ttu-id="777aa-445">O pacote de provedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) grava a saída de log usando a classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chamadas de método `Debug.WriteLine`).</span><span class="sxs-lookup"><span data-stu-id="777aa-445">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="777aa-446">No Linux, esse provedor grava logs em */var/log/message*.</span><span class="sxs-lookup"><span data-stu-id="777aa-446">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="777aa-447">Provedor de origem do evento</span><span class="sxs-lookup"><span data-stu-id="777aa-447">Event Source provider</span></span>

<span data-ttu-id="777aa-448">O pacote do provedor [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) grava em uma origem de evento entre plataformas com o nome `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="777aa-448">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="777aa-449">No Windows, o provedor usa o [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="777aa-449">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="777aa-450">O provedor de origem do evento é adicionado automaticamente quando `CreateDefaultBuilder` é chamado para compilar o host.</span><span class="sxs-lookup"><span data-stu-id="777aa-450">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="777aa-451">ferramentas de rastreamento dotnet</span><span class="sxs-lookup"><span data-stu-id="777aa-451">dotnet trace tooling</span></span>

<span data-ttu-id="777aa-452">A ferramenta [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) é uma ferramenta global da CLI de plataforma cruzada que habilita a coleta de rastreamentos do .NET Core de um processo em execução.</span><span class="sxs-lookup"><span data-stu-id="777aa-452">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="777aa-453">A ferramenta coleta dados de provedor de <xref:Microsoft.Extensions.Logging.EventSource> usando um <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="777aa-453">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="777aa-454">Instale as ferramentas de rastreamento dotnet com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="777aa-454">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="777aa-455">Use as ferramentas de rastreamento dotnet para coletar um rastreamento de um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="777aa-455">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="777aa-456">Se o aplicativo não criar o host com `CreateDefaultBuilder`, adicione o [provedor de origem de evento](#event-source-provider) à configuração de log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-456">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="777aa-457">Execute o aplicativo com o comando `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="777aa-457">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="777aa-458">Determine o identificador do processo (PID) do aplicativo .NET Core:</span><span class="sxs-lookup"><span data-stu-id="777aa-458">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="777aa-459">No Windows, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="777aa-459">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="777aa-460">Gerenciador de tarefas (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="777aa-460">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="777aa-461">comando tasklist</span><span class="sxs-lookup"><span data-stu-id="777aa-461">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="777aa-462">Comando do PowerShell Get-Process</span><span class="sxs-lookup"><span data-stu-id="777aa-462">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="777aa-463">No Linux, use o [comando pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="777aa-463">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="777aa-464">Localize o PID do processo que tem o mesmo nome que o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-464">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="777aa-465">Execute o comando `dotnet trace`.</span><span class="sxs-lookup"><span data-stu-id="777aa-465">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="777aa-466">Sintaxe de comando geral:</span><span class="sxs-lookup"><span data-stu-id="777aa-466">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="777aa-467">Ao usar um shell de comando do PowerShell, coloque o valor `--providers` entre aspas simples (`'`):</span><span class="sxs-lookup"><span data-stu-id="777aa-467">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="777aa-468">Em plataformas não Windows, adicione a opção `-f speedscope` para alterar o formato do arquivo de rastreamento de saída para `speedscope`.</span><span class="sxs-lookup"><span data-stu-id="777aa-468">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="777aa-469">Palavra-chave</span><span class="sxs-lookup"><span data-stu-id="777aa-469">Keyword</span></span> | <span data-ttu-id="777aa-470">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="777aa-470">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="777aa-471">1</span><span class="sxs-lookup"><span data-stu-id="777aa-471">1</span></span>       | <span data-ttu-id="777aa-472">Eventos meta de log sobre o `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="777aa-472">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="777aa-473">Não registra eventos de `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="777aa-473">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="777aa-474">2</span><span class="sxs-lookup"><span data-stu-id="777aa-474">2</span></span>       | <span data-ttu-id="777aa-475">Ativa o evento `Message` quando `ILogger.Log()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="777aa-475">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="777aa-476">Fornece informações em uma maneira programática (não formatada).</span><span class="sxs-lookup"><span data-stu-id="777aa-476">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="777aa-477">4</span><span class="sxs-lookup"><span data-stu-id="777aa-477">4</span></span>       | <span data-ttu-id="777aa-478">Ativa o evento `FormatMessage` quando `ILogger.Log()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="777aa-478">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="777aa-479">Fornece a versão de cadeia de caracteres formatada das informações.</span><span class="sxs-lookup"><span data-stu-id="777aa-479">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="777aa-480">8</span><span class="sxs-lookup"><span data-stu-id="777aa-480">8</span></span>       | <span data-ttu-id="777aa-481">Ativa o evento `MessageJson` quando `ILogger.Log()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="777aa-481">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="777aa-482">Fornece uma representação JSON dos argumentos.</span><span class="sxs-lookup"><span data-stu-id="777aa-482">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="777aa-483">Nível de evento</span><span class="sxs-lookup"><span data-stu-id="777aa-483">Event Level</span></span> | <span data-ttu-id="777aa-484">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="777aa-484">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="777aa-485">0</span><span class="sxs-lookup"><span data-stu-id="777aa-485">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="777aa-486">1</span><span class="sxs-lookup"><span data-stu-id="777aa-486">1</span></span>           | `Critical`      |
   | <span data-ttu-id="777aa-487">2</span><span class="sxs-lookup"><span data-stu-id="777aa-487">2</span></span>           | `Error`         |
   | <span data-ttu-id="777aa-488">3</span><span class="sxs-lookup"><span data-stu-id="777aa-488">3</span></span>           | `Warning`       |
   | <span data-ttu-id="777aa-489">4</span><span class="sxs-lookup"><span data-stu-id="777aa-489">4</span></span>           | `Informational` |
   | <span data-ttu-id="777aa-490">5</span><span class="sxs-lookup"><span data-stu-id="777aa-490">5</span></span>           | `Verbose`       |

   <span data-ttu-id="777aa-491">`FilterSpecs` entradas para `{Logger Category}` e `{Event Level}` representam condições de filtragem de log adicionais.</span><span class="sxs-lookup"><span data-stu-id="777aa-491">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="777aa-492">Separe `FilterSpecs` entradas com um ponto e vírgula (`;`).</span><span class="sxs-lookup"><span data-stu-id="777aa-492">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="777aa-493">Exemplo usando um shell de comando do Windows (**sem** aspas simples em relação ao valor `--providers`):</span><span class="sxs-lookup"><span data-stu-id="777aa-493">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="777aa-494">O comando anterior é ativado:</span><span class="sxs-lookup"><span data-stu-id="777aa-494">The preceding command activates:</span></span>

   * <span data-ttu-id="777aa-495">O agente de log de origem do evento para produzir cadeias de caracteres formatadas (`4`) para erros (`2`).</span><span class="sxs-lookup"><span data-stu-id="777aa-495">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="777aa-496">`Microsoft.AspNetCore.Hosting` registro em log no nível de log de `Informational` (`4`).</span><span class="sxs-lookup"><span data-stu-id="777aa-496">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="777aa-497">Pare as ferramentas de rastreamento dotnet pressionando a tecla Enter ou CTRL + C.</span><span class="sxs-lookup"><span data-stu-id="777aa-497">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="777aa-498">O rastreamento é salvo com o nome *trace. NetTrace* na pasta em que o comando `dotnet trace` é executado.</span><span class="sxs-lookup"><span data-stu-id="777aa-498">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="777aa-499">Abra o rastreamento com [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="777aa-499">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="777aa-500">Abra o arquivo *trace. NetTrace* e explore os eventos de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="777aa-500">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="777aa-501">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="777aa-501">For more information, see:</span></span>

* <span data-ttu-id="777aa-502">[Rastreamento do utilitário de análise de desempenho (dotNet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (documentação do .NET Core)</span><span class="sxs-lookup"><span data-stu-id="777aa-502">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="777aa-503">[Rastreamento do utilitário de análise de desempenho (dotNet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (documentação do repositório do GitHub de dotnet/diagnóstico)</span><span class="sxs-lookup"><span data-stu-id="777aa-503">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="777aa-504">[Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (navegador de API .net)</span><span class="sxs-lookup"><span data-stu-id="777aa-504">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="777aa-505">[Fonte de referência LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; para obter a fonte de referência para uma versão diferente, altere a ramificação para `release/{Version}`, em que `{Version}` é a versão do ASP.NET Core desejado.</span><span class="sxs-lookup"><span data-stu-id="777aa-505">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="777aa-506">[Perfview](#perfview) &ndash; útil para exibir rastreamentos de origem do evento.</span><span class="sxs-lookup"><span data-stu-id="777aa-506">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="777aa-507">Perfview</span><span class="sxs-lookup"><span data-stu-id="777aa-507">Perfview</span></span>

::: moniker-end

<span data-ttu-id="777aa-508">Use o [utilitário Perfview](https://github.com/Microsoft/perfview) para coletar e exibir logs.</span><span class="sxs-lookup"><span data-stu-id="777aa-508">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="777aa-509">Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="777aa-509">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="777aa-510">Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**.</span><span class="sxs-lookup"><span data-stu-id="777aa-510">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="777aa-511">(Não se esqueça do asterisco no início da cadeia de caracteres).</span><span class="sxs-lookup"><span data-stu-id="777aa-511">(Don't miss the asterisk at the start of the string.)</span></span>

![Outros provedores de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="777aa-513">Provedor EventLog do Windows</span><span class="sxs-lookup"><span data-stu-id="777aa-513">Windows EventLog provider</span></span>

<span data-ttu-id="777aa-514">O pacote de provedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envia a saída de log para o Log de Eventos do Windows.</span><span class="sxs-lookup"><span data-stu-id="777aa-514">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="777aa-515">As [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permitem que você passe <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span><span class="sxs-lookup"><span data-stu-id="777aa-515">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="777aa-516">Se `null` ou não for especificado, as seguintes configurações padrão serão usadas:</span><span class="sxs-lookup"><span data-stu-id="777aa-516">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="777aa-517">`LogName` &ndash; "aplicativo"</span><span class="sxs-lookup"><span data-stu-id="777aa-517">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="777aa-518">`SourceName` &ndash; ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="777aa-518">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="777aa-519">`MachineName` &ndash; computador local</span><span class="sxs-lookup"><span data-stu-id="777aa-519">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="777aa-520">Os eventos são registrados em log para o [nível de aviso e superior](#log-level).</span><span class="sxs-lookup"><span data-stu-id="777aa-520">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="777aa-521">Para registrar em log eventos inferiores a `Warning`, defina explicitamente o nível de log.</span><span class="sxs-lookup"><span data-stu-id="777aa-521">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="777aa-522">Por exemplo, adicione o seguinte ao arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="777aa-522">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="777aa-523">Provedor TraceSource</span><span class="sxs-lookup"><span data-stu-id="777aa-523">TraceSource provider</span></span>

<span data-ttu-id="777aa-524">O pacote de provedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa as bibliotecas e provedores de <xref:System.Diagnostics.TraceSource>.</span><span class="sxs-lookup"><span data-stu-id="777aa-524">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="777aa-525">As [sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permitem que você passe um comutador de fonte e um ouvinte de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="777aa-525">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="777aa-526">Para usar esse provedor, o aplicativo deve ser executado no .NET Framework (em vez do .NET Core).</span><span class="sxs-lookup"><span data-stu-id="777aa-526">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="777aa-527">O provedor pode rotear mensagens a uma variedade de [ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners), como o <xref:System.Diagnostics.TextWriterTraceListener> usado no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="777aa-527">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="777aa-528">Provedor do Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="777aa-528">Azure App Service provider</span></span>

<span data-ttu-id="777aa-529">O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure.</span><span class="sxs-lookup"><span data-stu-id="777aa-529">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="777aa-530">O pacote do provedor não está incluído na estrutura compartilhada.</span><span class="sxs-lookup"><span data-stu-id="777aa-530">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="777aa-531">Para usar o provedor, adicione o pacote do provedor ao projeto.</span><span class="sxs-lookup"><span data-stu-id="777aa-531">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="777aa-532">O pacote de provedor não está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="777aa-532">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="777aa-533">Se você estiver direcionando para o .NET Framework ou referenciando o metapacote `Microsoft.AspNetCore.App`, adicione o pacote do provedor ao projeto.</span><span class="sxs-lookup"><span data-stu-id="777aa-533">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="777aa-534">Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="777aa-534">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="777aa-535">Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="777aa-535">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="777aa-536">Uma sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite passar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="777aa-536">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="777aa-537">O objeto settings pode substituir as configurações padrão, como o modelo de saída de registro em log, o nome do blob e o limite do tamanho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="777aa-537">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="777aa-538">(O *modelo Output* é um modelo de mensagem aplicado a todos os logs, além daquele fornecido com uma chamada ao método `ILogger`).</span><span class="sxs-lookup"><span data-stu-id="777aa-538">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="777aa-539">Quando você implanta em um aplicativo do Serviço de Aplicativo, o aplicativo respeita as configurações na seção [Logs do Serviço de Aplicativo](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) da página **Serviço de Aplicativo** do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="777aa-539">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="777aa-540">Quando as configurações a seguir são atualizadas, as alterações entram em vigor imediatamente sem a necessidade de uma reinicialização ou reimplantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-540">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="777aa-541">**Log de aplicativo (Sistema de Arquivos)**</span><span class="sxs-lookup"><span data-stu-id="777aa-541">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="777aa-542">**Log de aplicativo (Blob)**</span><span class="sxs-lookup"><span data-stu-id="777aa-542">**Application Logging (Blob)**</span></span>

<span data-ttu-id="777aa-543">O local padrão para arquivos de log é na pasta *D:\\home\\LogFiles\\Application* e o nome de arquivo padrão é *diagnostics-aaaammdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="777aa-543">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="777aa-544">O limite padrão de tamanho do arquivo é 10 MB e o número padrão máximo de arquivos mantidos é 2.</span><span class="sxs-lookup"><span data-stu-id="777aa-544">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="777aa-545">O nome de blob padrão é *{app-name}{timestamp}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="777aa-545">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="777aa-546">O provedor funciona somente quando o projeto é executado no ambiente do Azure.</span><span class="sxs-lookup"><span data-stu-id="777aa-546">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="777aa-547">Ele não tem nenhum efeito quando o projeto é executado localmente&mdash;ele não grava em arquivos locais ou no armazenamento de desenvolvimento local para blobs.</span><span class="sxs-lookup"><span data-stu-id="777aa-547">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="777aa-548">Fluxo de log do Azure</span><span class="sxs-lookup"><span data-stu-id="777aa-548">Azure log streaming</span></span>

<span data-ttu-id="777aa-549">O fluxo de log do Azure permite que você exiba a atividade de log em tempo real:</span><span class="sxs-lookup"><span data-stu-id="777aa-549">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="777aa-550">O servidor de aplicativos</span><span class="sxs-lookup"><span data-stu-id="777aa-550">The app server</span></span>
* <span data-ttu-id="777aa-551">Do servidor Web</span><span class="sxs-lookup"><span data-stu-id="777aa-551">The web server</span></span>
* <span data-ttu-id="777aa-552">De uma solicitação de rastreio com falha</span><span class="sxs-lookup"><span data-stu-id="777aa-552">Failed request tracing</span></span>

<span data-ttu-id="777aa-553">Para configurar o fluxo de log do Azure:</span><span class="sxs-lookup"><span data-stu-id="777aa-553">To configure Azure log streaming:</span></span>

* <span data-ttu-id="777aa-554">Navegue até a página **Logs do Serviço de Aplicativo** da página do portal do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-554">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="777aa-555">Defina **Habilitar o log de aplicativo (sistema de arquivos)** como **Ativada**.</span><span class="sxs-lookup"><span data-stu-id="777aa-555">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="777aa-556">Escolha o **Nível** de log.</span><span class="sxs-lookup"><span data-stu-id="777aa-556">Choose the log **Level**.</span></span> <span data-ttu-id="777aa-557">Essa configuração se aplica somente ao streaming de log do Azure, não a outros provedores de log no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-557">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="777aa-558">Navegue até a página **Fluxo de Log** para exibir as mensagens de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="777aa-558">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="777aa-559">Elas são registradas pelo aplicativo por meio da interface `ILogger`.</span><span class="sxs-lookup"><span data-stu-id="777aa-559">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="777aa-560">Log de rastreamento do Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="777aa-560">Azure Application Insights trace logging</span></span>

<span data-ttu-id="777aa-561">O pacote de provedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) grava os logs no Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="777aa-561">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="777aa-562">O Application Insights é um serviço que monitora um aplicativo web e fornece ferramentas para consultar e analisar os dados de telemetria.</span><span class="sxs-lookup"><span data-stu-id="777aa-562">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="777aa-563">Se você usar esse provedor, poderá consultar e analisar os logs usando as ferramentas do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="777aa-563">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="777aa-564">O provedor de registro em log está incluído como uma dependência de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que é o pacote que fornece toda a telemetria disponível para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="777aa-564">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="777aa-565">Se você usar esse pacote, não precisará instalar o pacote de provedor.</span><span class="sxs-lookup"><span data-stu-id="777aa-565">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="777aa-566">Não use o pacote [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)&mdash;que é para o ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="777aa-566">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="777aa-567">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="777aa-567">For more information, see the following resources:</span></span>

* [<span data-ttu-id="777aa-568">Visão geral do Application Insights</span><span class="sxs-lookup"><span data-stu-id="777aa-568">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="777aa-569">[Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core) – Comece aqui se você deseja implementar toda a gama de telemetria do Application Insights junto com o registro em log.</span><span class="sxs-lookup"><span data-stu-id="777aa-569">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="777aa-570">[ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Comece aqui se você quiser implementar o provedor de log sem o restante da telemetria do Application Insights.</span><span class="sxs-lookup"><span data-stu-id="777aa-570">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="777aa-571">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs) (Adaptadores de registro em log do Application Insights).</span><span class="sxs-lookup"><span data-stu-id="777aa-571">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="777aa-572">[Instalar, configurar e inicializar o SDK do Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Tutorial interativo no site da Microsoft Learn.</span><span class="sxs-lookup"><span data-stu-id="777aa-572">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="777aa-573">Provedores de log de terceiros</span><span class="sxs-lookup"><span data-stu-id="777aa-573">Third-party logging providers</span></span>

<span data-ttu-id="777aa-574">Estruturas de log de terceiros que funcionam com o ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="777aa-574">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="777aa-575">[elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="777aa-575">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="777aa-576">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="777aa-576">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="777aa-577">[JSNLog](https://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="777aa-577">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="777aa-578">[KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="777aa-578">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="777aa-579">[Log4net](https://logging.apache.org/log4net/) ([repositório GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="777aa-579">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="777aa-580">[Loggr](https://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="777aa-580">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="777aa-581">[NLog](https://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="777aa-581">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="777aa-582">[Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="777aa-582">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="777aa-583">[Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="777aa-583">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="777aa-584">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="777aa-584">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="777aa-585">Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="777aa-585">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="777aa-586">Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:</span><span class="sxs-lookup"><span data-stu-id="777aa-586">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="777aa-587">Adicione um pacote NuGet ao projeto.</span><span class="sxs-lookup"><span data-stu-id="777aa-587">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="777aa-588">Chame um método de extensão de `ILoggerFactory` fornecido pela estrutura de log.</span><span class="sxs-lookup"><span data-stu-id="777aa-588">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="777aa-589">Para saber mais, consulte a documentação de cada provedor.</span><span class="sxs-lookup"><span data-stu-id="777aa-589">For more information, see each provider's documentation.</span></span> <span data-ttu-id="777aa-590">Não há suporte para provedores de log de terceiros na Microsoft.</span><span class="sxs-lookup"><span data-stu-id="777aa-590">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="777aa-591">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="777aa-591">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
