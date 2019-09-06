---
title: Inicialização de aplicativo no ASP.NET Core
author: rick-anderson
description: Saiba como a classe Startup no ASP.NET Core configura serviços e o pipeline de solicitação do aplicativo.
ms.author: riande
ms.custom: mvc
ms.date: 8/7/2019
uid: fundamentals/startup
ms.openlocfilehash: 9407de4ee91ba43b2c95fa98f0cf479bf8539cab
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310491"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="4e812-103">Inicialização de aplicativo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e812-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="4e812-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="4e812-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="4e812-105">A classe `Startup` configura serviços e o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="4e812-106">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="4e812-106">The Startup class</span></span>

<span data-ttu-id="4e812-107">Os aplicativos do ASP.NET Core usam uma classe `Startup`, que é chamada de `Startup` por convenção.</span><span class="sxs-lookup"><span data-stu-id="4e812-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="4e812-108">A classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="4e812-108">The `Startup` class:</span></span>

* <span data-ttu-id="4e812-109">Opcionalmente, inclua um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> para configurar os *serviços* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="4e812-110">Um serviço é um componente reutilizável que fornece a funcionalidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="4e812-111">Os serviços estão configurados&mdash;também descritos como *registrados*&mdash;em `ConfigureServices` e consumidos no aplicativo por meio da [DI (injeção de dependência)](xref:fundamentals/dependency-injection) ou <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="4e812-111">Services are configured&mdash;also described as *registered*&mdash;in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="4e812-112">Inclui um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> para criar o pipeline de processamento de solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="4e812-113">`ConfigureServices` e `Configure` são chamados pelo tempo de execução do ASP.NET Core quando o aplicativo é iniciado:</span><span class="sxs-lookup"><span data-stu-id="4e812-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="4e812-114">O exemplo anterior é para [Razor Pages](xref:razor-pages/index); a versão MVC é semelhante.</span><span class="sxs-lookup"><span data-stu-id="4e812-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

<span data-ttu-id="4e812-115">A classe `Startup` é especificada quando o [host](xref:fundamentals/index#host) do aplicativo é criado.</span><span class="sxs-lookup"><span data-stu-id="4e812-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="4e812-116">A classe `Startup` normalmente é especificada chamando o método [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) no construtor de host:</span><span class="sxs-lookup"><span data-stu-id="4e812-116">The `Startup` class is typically specified by calling the [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="4e812-117">O host fornece serviços que estão disponíveis para o construtor de classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4e812-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="4e812-118">O aplicativo adiciona serviços adicionais por meio de `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4e812-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="4e812-119">Os serviços de aplicativos e o host ficam disponíveis em `Configure` e em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="4e812-120">Somente os seguintes tipos de serviço podem ser injetados no construtor `Startup` ao usar <xref:Microsoft.Extensions.Hosting.IHostBuilder>:</span><span class="sxs-lookup"><span data-stu-id="4e812-120">Only the following service types can be injected into the `Startup` constructor when using <xref:Microsoft.Extensions.Hosting.IHostBuilder>:</span></span>

* `IWebHostEnvironment`
* `IHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="4e812-121">A maioria dos serviços não está disponível até o método `Configure` ser chamado.</span><span class="sxs-lookup"><span data-stu-id="4e812-121">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e812-122">O host fornece serviços que estão disponíveis para o construtor de classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="4e812-122">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="4e812-123">O aplicativo adiciona serviços adicionais por meio de `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4e812-123">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="4e812-124">Os serviços de aplicativos e o host ficam, então, disponíveis em `Configure` e em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-124">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="4e812-125">Um uso comum da [injeção de dependência](xref:fundamentals/dependency-injection) na classe `Startup` é injetar:</span><span class="sxs-lookup"><span data-stu-id="4e812-125">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="4e812-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para configurar serviços pelo ambiente.</span><span class="sxs-lookup"><span data-stu-id="4e812-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="4e812-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> para ler a configuração.</span><span class="sxs-lookup"><span data-stu-id="4e812-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="4e812-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> para criar um agente em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4e812-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="4e812-129">A maioria dos serviços não está disponível até o método `Configure` ser chamado.</span><span class="sxs-lookup"><span data-stu-id="4e812-129">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

### <a name="multiple-startup"></a><span data-ttu-id="4e812-130">Inicialização múltipla</span><span class="sxs-lookup"><span data-stu-id="4e812-130">Multiple StartUp</span></span>

<span data-ttu-id="4e812-131">Quando o aplicativo define classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`), a classe `Startup` apropriada é selecionada no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="4e812-131">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="4e812-132">A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada.</span><span class="sxs-lookup"><span data-stu-id="4e812-132">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="4e812-133">Se o aplicativo for executado no ambiente de desenvolvimento e incluir uma classe `Startup` e uma classe `StartupDevelopment`, a classe `StartupDevelopment` será usada.</span><span class="sxs-lookup"><span data-stu-id="4e812-133">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="4e812-134">Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="4e812-134">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="4e812-135">Veja [O host](xref:fundamentals/index#host) para obter mais informações sobre o host.</span><span class="sxs-lookup"><span data-stu-id="4e812-135">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="4e812-136">Para obter informações sobre como tratar erros durante a inicialização, consulte [Tratamento de exceção na inicialização](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="4e812-136">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="4e812-137">O método ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="4e812-137">The ConfigureServices method</span></span>

<span data-ttu-id="4e812-138">O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> é:</span><span class="sxs-lookup"><span data-stu-id="4e812-138">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="4e812-139">Opcional.</span><span class="sxs-lookup"><span data-stu-id="4e812-139">Optional.</span></span>
* <span data-ttu-id="4e812-140">Chamado pelo host antes do método `Configure` para configurar os serviços do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-140">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="4e812-141">Onde as [opções de configuração](xref:fundamentals/configuration/index) são definidas por convenção.</span><span class="sxs-lookup"><span data-stu-id="4e812-141">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="4e812-142">O host pode configurar alguns serviços antes que métodos `Startup` sejam chamados.</span><span class="sxs-lookup"><span data-stu-id="4e812-142">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="4e812-143">Para obter mais informações, confira [O host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="4e812-143">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="4e812-144">Para recursos que exigem uma configuração significativa, há métodos de extensão `Add{Service}` em <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span><span class="sxs-lookup"><span data-stu-id="4e812-144">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="4e812-145">Por exemplo, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores e **Add**RazorPages:</span><span class="sxs-lookup"><span data-stu-id="4e812-145">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

<span data-ttu-id="4e812-146">Adicionar serviços ao contêiner de serviços os torna disponíveis dentro do aplicativo e no método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="4e812-146">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="4e812-147">Os serviços são resolvidos por meio da [injeção de dependência](xref:fundamentals/dependency-injection) ou de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="4e812-147">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e812-148">Confira [SetCompatibilityVersion](xref:mvc/compatibility-version) para obter mais informações sobre `SetCompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="4e812-148">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

::: moniker-end

## <a name="the-configure-method"></a><span data-ttu-id="4e812-149">O método Configure</span><span class="sxs-lookup"><span data-stu-id="4e812-149">The Configure method</span></span>

<span data-ttu-id="4e812-150">O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> é usado para especificar como o aplicativo responde às solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="4e812-150">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="4e812-151">O pipeline de solicitação é configurado adicionando componentes de [middleware](xref:fundamentals/middleware/index) a uma instância de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4e812-151">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="4e812-152">`IApplicationBuilder` está disponível para o método `Configure`, mas não é registrado no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4e812-152">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="4e812-153">A hospedagem cria um `IApplicationBuilder` e o passa diretamente para `Configure`.</span><span class="sxs-lookup"><span data-stu-id="4e812-153">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="4e812-154">Os [modelos do ASP.NET Core](/dotnet/core/tools/dotnet-new) configuram o pipeline com suporte para:</span><span class="sxs-lookup"><span data-stu-id="4e812-154">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="4e812-155">Página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="4e812-155">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="4e812-156">Manipulador de exceção</span><span class="sxs-lookup"><span data-stu-id="4e812-156">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="4e812-157">Segurança de Transporte Estrita de HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="4e812-157">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="4e812-158">Redirecionamento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="4e812-158">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="4e812-159">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="4e812-159">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="4e812-160">[MVC](xref:mvc/overview) do ASP.NET Core e [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="4e812-160">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="4e812-161">RGPD (Regulamento Geral sobre a Proteção de Dados) da UE</span><span class="sxs-lookup"><span data-stu-id="4e812-161">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="4e812-162">O exemplo anterior é para [Razor Pages](xref:razor-pages/index); a versão MVC é semelhante.</span><span class="sxs-lookup"><span data-stu-id="4e812-162">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

<span data-ttu-id="4e812-163">Cada método de extensão `Use` adiciona um ou mais componentes de middleware ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="4e812-163">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="4e812-164">Por exemplo, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configura o [middleware](xref:fundamentals/middleware/index) para atender [arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="4e812-164">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="4e812-165">Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito da cadeia, se apropriado.</span><span class="sxs-lookup"><span data-stu-id="4e812-165">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e812-166">Serviços adicionais, como `IWebHostEnvironment`, `ILoggerFactory` ou qualquer coisa definida em `ConfigureServices`, podem ser especificados na assinatura do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="4e812-166">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="4e812-167">Esses serviços serão injetados se estiverem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="4e812-167">These services are injected if they're available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e812-168">Serviços adicionais, como `IHostingEnvironment` e `ILoggerFactory` ou qualquer coisa definida em `ConfigureServices`, podem ser especificados na assinatura do método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="4e812-168">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="4e812-169">Esses serviços serão injetados se estiverem disponíveis.</span><span class="sxs-lookup"><span data-stu-id="4e812-169">These services are injected if they're available.</span></span>

::: moniker-end

<span data-ttu-id="4e812-170">Para obter mais informações de como usar o `IApplicationBuilder` e a ordem de processamento de middleware, confira <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="4e812-170">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="4e812-171">Configurar serviços sem Inicialização</span><span class="sxs-lookup"><span data-stu-id="4e812-171">Configure services without Startup</span></span>

<span data-ttu-id="4e812-172">Para configurar serviços e o pipeline de processamento de solicitação sem usar uma classe `Startup`, chame os métodos de conveniência `ConfigureServices` e `Configure` no construtor do host.</span><span class="sxs-lookup"><span data-stu-id="4e812-172">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="4e812-173">Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="4e812-173">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="4e812-174">Se houver várias chamadas de método `Configure`, a última chamada de `Configure` será usada.</span><span class="sxs-lookup"><span data-stu-id="4e812-174">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="4e812-175">Estender a inicialização com filtros de inicialização</span><span class="sxs-lookup"><span data-stu-id="4e812-175">Extend Startup with startup filters</span></span>

<span data-ttu-id="4e812-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> para configurar o middleware no início ou no final do pipeline do middleware [Configure](#the-configure-method) de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> to configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline.</span></span> <span data-ttu-id="4e812-177">`IStartupFilter` é usado para criar um pipeline de métodos `Configure`.</span><span class="sxs-lookup"><span data-stu-id="4e812-177">`IStartupFilter` is used to create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="4e812-178">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) pode definir um middleware para ser executado antes ou depois do middleware adicionado pelas bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="4e812-178">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="4e812-179">`IStartupFilter` implementa <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, que recebe e retorna um `Action<IApplicationBuilder>`.</span><span class="sxs-lookup"><span data-stu-id="4e812-179">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="4e812-180">Um <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> define uma classe para configurar o pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-180">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="4e812-181">Para obter mais informações, confira [Criar um pipeline de middleware com o IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="4e812-181">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="4e812-182">Cada `IStartupFilter` pode adicionar um ou mais middlewares no pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="4e812-182">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="4e812-183">Os filtros são invocados na ordem em que foram adicionados ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4e812-183">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="4e812-184">Filtros podem adicionar middleware antes ou depois de passar o controle para o filtro seguinte, de modo que eles são acrescentados ao início ou no final do pipeline de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-184">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="4e812-185">O exemplo a seguir demonstra como registrar um middleware com `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="4e812-185">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="4e812-186">O middleware `RequestSetOptionsMiddleware` define um valor de opção de um parâmetro de cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="4e812-186">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="4e812-187">O `RequestSetOptionsMiddleware` é configurado na classe `RequestSetOptionsStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="4e812-187">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="4e812-188">O `RequestSetOptionsMiddleware` é configurado na classe `RequestSetOptionsStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="4e812-188">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

<span data-ttu-id="4e812-189">O `IStartupFilter` é registrado no contêiner de serviço em <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="4e812-189">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

<span data-ttu-id="4e812-190">Quando um parâmetro de cadeia de caracteres de consulta para `option` é fornecido, o middleware processa a atribuição de valor antes que o middleware do ASP.NET Core renderize a resposta.</span><span class="sxs-lookup"><span data-stu-id="4e812-190">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="4e812-191">A ordem de execução do middleware é definida pela ordem dos registros `IStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="4e812-191">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="4e812-192">Várias implementações de `IStartupFilter` podem interagir com os mesmos objetos.</span><span class="sxs-lookup"><span data-stu-id="4e812-192">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="4e812-193">Se a ordem for importante, ordene seus registros de serviço `IStartupFilter` para corresponder à ordem em que os middlewares devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="4e812-193">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="4e812-194">Bibliotecas podem adicionar middleware com uma ou mais implementações de `IStartupFilter` que são executadas antes ou depois de outro middleware de aplicativo registrado com `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="4e812-194">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="4e812-195">Para invocar um middleware `IStartupFilter` antes de um middleware adicionado pelo `IStartupFilter` de uma biblioteca:</span><span class="sxs-lookup"><span data-stu-id="4e812-195">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="4e812-196">Posicione o registro do serviço antes que a biblioteca seja adicionada ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="4e812-196">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="4e812-197">Para invocá-lo posteriormente, posicione o registro do serviço após a biblioteca ser adicionada.</span><span class="sxs-lookup"><span data-stu-id="4e812-197">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="4e812-198">Adicionar configuração na inicialização usando um assembly externo</span><span class="sxs-lookup"><span data-stu-id="4e812-198">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="4e812-199">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4e812-199">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4e812-200">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4e812-200">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e812-201">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4e812-201">Additional resources</span></span>

* [<span data-ttu-id="4e812-202">O host</span><span class="sxs-lookup"><span data-stu-id="4e812-202">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
