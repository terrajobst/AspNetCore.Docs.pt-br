---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Descubra os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/index
ms.openlocfilehash: 11dc6336ae7667038983c967f28232bef325f5bb
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637762"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="91932-103">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91932-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="91932-104">Um aplicativo ASP.NET Core é um aplicativo de console que cria um servidor Web em seu método `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="91932-104">An ASP.NET Core app is a console app that creates a web server in its `Program.Main` method.</span></span> <span data-ttu-id="91932-105">O método `Main` é o *ponto de entrada gerenciado* do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="91932-105">The `Main` method is the app's *managed entry point*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs)]

<span data-ttu-id="91932-106">O host do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="91932-106">The .NET Core Host:</span></span>

* <span data-ttu-id="91932-107">Carrega o [tempo de execução do .NET Core](https://github.com/dotnet/coreclr).</span><span class="sxs-lookup"><span data-stu-id="91932-107">Loads the [.NET Core runtime](https://github.com/dotnet/coreclr).</span></span>
* <span data-ttu-id="91932-108">Usa o primeiro argumento de linha de comando como o caminho para o binário gerenciado que contém o ponto de entrada (`Main`) e inicia a execução do código.</span><span class="sxs-lookup"><span data-stu-id="91932-108">Uses the first command-line argument as the path to the managed binary that contains the entry point (`Main`) and begins code execution.</span></span>

<span data-ttu-id="91932-109">O método `Main` invoca [WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*), que segue o [padrão de construtor](https://wikipedia.org/wiki/Builder_pattern) para criar um host da Web.</span><span class="sxs-lookup"><span data-stu-id="91932-109">The `Main` method invokes [WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*), which follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern) to create a web host.</span></span> <span data-ttu-id="91932-110">O construtor tem métodos que definem um servidor Web (por exemplo, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) e a classe de inicialização (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span><span class="sxs-lookup"><span data-stu-id="91932-110">The builder has methods that define a web server (for example, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) and the startup class (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span></span> <span data-ttu-id="91932-111">No exemplo anterior, o servidor Web [Kestrel](xref:fundamentals/servers/kestrel) é alocado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="91932-111">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is automatically allocated.</span></span> <span data-ttu-id="91932-112">O host Web do ASP.NET Core tenta executar no [IIS (Serviços de Informações da Internet)](https://www.iis.net/), se disponível.</span><span class="sxs-lookup"><span data-stu-id="91932-112">ASP.NET Core's web host attempts to run on [Internet Information Services (IIS)](https://www.iis.net/), if available.</span></span> <span data-ttu-id="91932-113">Outros servidores Web como [HTTP.sys](xref:fundamentals/servers/httpsys) podem ser usados ao chamar o método de extensão apropriado.</span><span class="sxs-lookup"><span data-stu-id="91932-113">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="91932-114">`UseStartup` é explicado em mais detalhes na seção [Inicialização](#startup).</span><span class="sxs-lookup"><span data-stu-id="91932-114">`UseStartup` is explained further in the [Startup](#startup) section.</span></span>

<span data-ttu-id="91932-115"><xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, o tipo de retorno da invocação de `WebHost.CreateDefaultBuilder`, fornece muitos métodos opcionais.</span><span class="sxs-lookup"><span data-stu-id="91932-115"><xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, the return type of the `WebHost.CreateDefaultBuilder` invocation, provides many optional methods.</span></span> <span data-ttu-id="91932-116">Alguns desses métodos incluem `UseHttpSys` para hospedar o aplicativo em HTTP.sys e <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> para especificar o diretório de conteúdo raiz.</span><span class="sxs-lookup"><span data-stu-id="91932-116">Some of these methods include `UseHttpSys` for hosting the app in HTTP.sys and <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> for specifying the root content directory.</span></span> <span data-ttu-id="91932-117">Os métodos <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> e <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> compilam o objeto <xref:Microsoft.AspNetCore.Hosting.IWebHost> que hospeda o aplicativo e começa a escutar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="91932-117">The <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> and <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> methods build the <xref:Microsoft.AspNetCore.Hosting.IWebHost> object that hosts the app and begins listening for HTTP requests.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs)]

<span data-ttu-id="91932-118">O host do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="91932-118">The .NET Core Host:</span></span>

* <span data-ttu-id="91932-119">Carrega o [tempo de execução do .NET Core](https://github.com/dotnet/coreclr).</span><span class="sxs-lookup"><span data-stu-id="91932-119">Loads the [.NET Core runtime](https://github.com/dotnet/coreclr).</span></span>
* <span data-ttu-id="91932-120">Usa o primeiro argumento de linha de comando como o caminho para o binário gerenciado que contém o ponto de entrada (`Main`) e inicia a execução do código.</span><span class="sxs-lookup"><span data-stu-id="91932-120">Uses the first command-line argument as the path to the managed binary that contains the entry point (`Main`) and begins code execution.</span></span>

<span data-ttu-id="91932-121">O método `Main` usa <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, que segue o [padrão de construtor](https://wikipedia.org/wiki/Builder_pattern) para criar um host de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="91932-121">The `Main` method uses <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, which follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern) to create a web app host.</span></span> <span data-ttu-id="91932-122">O construtor tem métodos que definem o servidor Web (por exemplo, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) e a classe de inicialização (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span><span class="sxs-lookup"><span data-stu-id="91932-122">The builder has methods that define the web server (for example, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) and the startup class (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span></span> <span data-ttu-id="91932-123">No exemplo anterior, o servidor Web [Kestrel](xref:fundamentals/servers/kestrel) é usado.</span><span class="sxs-lookup"><span data-stu-id="91932-123">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is used.</span></span> <span data-ttu-id="91932-124">Outros servidores Web como [HTTP.sys](xref:fundamentals/servers/httpsys) podem ser usados ao chamar o método de extensão apropriado.</span><span class="sxs-lookup"><span data-stu-id="91932-124">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="91932-125">`UseStartup` é explicado em mais detalhes na seção [Inicialização](#startup).</span><span class="sxs-lookup"><span data-stu-id="91932-125">`UseStartup` is explained further in the [Startup](#startup) section.</span></span>

<span data-ttu-id="91932-126">O `WebHostBuilder` fornece muitos métodos opcionais, incluindo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>, para hospedagem no IIS e no IIS Express, e <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*>, para especificar o diretório do conteúdo raiz.</span><span class="sxs-lookup"><span data-stu-id="91932-126">`WebHostBuilder` provides many optional methods, including <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> for hosting in IIS and IIS Express and <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> for specifying the root content directory.</span></span> <span data-ttu-id="91932-127">Os métodos <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> e <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> compilam o objeto <xref:Microsoft.AspNetCore.Hosting.IWebHost> que hospeda o aplicativo e começa a escutar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="91932-127">The <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> and <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> methods build the <xref:Microsoft.AspNetCore.Hosting.IWebHost> object that hosts the app and begins listening for HTTP requests.</span></span>

::: moniker-end

## <a name="startup"></a><span data-ttu-id="91932-128">Inicialização</span><span class="sxs-lookup"><span data-stu-id="91932-128">Startup</span></span>

<span data-ttu-id="91932-129">O método `UseStartup` em `WebHostBuilder` especifica a classe `Startup` para seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="91932-129">The `UseStartup` method on `WebHostBuilder` specifies the `Startup` class for your app:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs?highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs?highlight=7)]

::: moniker-end

<span data-ttu-id="91932-130">É na classe `Startup` que você define o pipeline de tratamento de solicitação e é nela que todos os serviços que o aplicativo precisa estão configurados.</span><span class="sxs-lookup"><span data-stu-id="91932-130">The `Startup` class is where you define the request handling pipeline and where any services needed by the app are configured.</span></span> <span data-ttu-id="91932-131">A classe `Startup` deve ser pública e conter os seguintes métodos:</span><span class="sxs-lookup"><span data-stu-id="91932-131">The `Startup` class must be public and contain the following methods:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Startup.cs)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Startup.cs)]

::: moniker-end

<span data-ttu-id="91932-132"><xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> define os [Serviços](#dependency-injection-services) usados pelo seu aplicativo (por exemplo, ASP.NET Core MVC, Entity Framework Core, Identity etc.).</span><span class="sxs-lookup"><span data-stu-id="91932-132"><xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> defines the [Services](#dependency-injection-services) used by your app (for example, ASP.NET Core MVC, Entity Framework Core, Identity).</span></span> <span data-ttu-id="91932-133"><xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> define o [middleware](xref:fundamentals/middleware/index) chamado no pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="91932-133"><xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> defines the [middleware](xref:fundamentals/middleware/index) called in the request pipeline.</span></span>

<span data-ttu-id="91932-134">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="91932-134">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="content-root"></a><span data-ttu-id="91932-135">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="91932-135">Content root</span></span>

<span data-ttu-id="91932-136">A raiz do conteúdo é o caminho base para qualquer conteúdo usado pelo aplicativo, tal como [Razor Pages](xref:razor-pages/index), exibições do MVC e ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="91932-136">The content root is the base path to any content used by the app, such as [Razor Pages](xref:razor-pages/index), MVC views, and static assets.</span></span> <span data-ttu-id="91932-137">Por padrão, a raiz do conteúdo é a mesma localização que o caminho base do aplicativo para o executável que hospeda o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="91932-137">By default, the content root is the same location as the app base path for the executable hosting the app.</span></span>

## <a name="web-root-webroot"></a><span data-ttu-id="91932-138">Diretório base (webroot)</span><span class="sxs-lookup"><span data-stu-id="91932-138">Web root (webroot)</span></span>

<span data-ttu-id="91932-139">O webroot de um aplicativo é o diretório do projeto que contém recursos públicos e estáticos como CSS, JavaScript e arquivos de imagem.</span><span class="sxs-lookup"><span data-stu-id="91932-139">The webroot of an app is the directory in the project containing public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="91932-140">Por padrão, *wwwroot* é o webroot.</span><span class="sxs-lookup"><span data-stu-id="91932-140">By default, *wwwroot* is the webroot.</span></span>

<span data-ttu-id="91932-141">Para arquivos (*.cshtml*) do Razor, o til-barra `~/` aponta para o webroot.</span><span class="sxs-lookup"><span data-stu-id="91932-141">For Razor (*.cshtml*) files, the tilde-slash  `~/` points to the webroot.</span></span> <span data-ttu-id="91932-142">Caminhos que começam com `~/` são denominados caminhos virtuais.</span><span class="sxs-lookup"><span data-stu-id="91932-142">Paths beginning with `~/` are referred to as virtual paths.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="91932-143">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="91932-143">Dependency injection (services)</span></span>

<span data-ttu-id="91932-144">Um *serviço* é um componente que é destinado ao consumo comum em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="91932-144">A *service* is a component that's intended for common consumption in an app.</span></span> <span data-ttu-id="91932-145">Os serviços são disponibilizados por meio de DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="91932-145">Services are made available through [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="91932-146">O ASP.NET Core inclui um contêiner nativo de IoC (Inversão de Controle) que dá suporte à [injeção de construtor](xref:mvc/controllers/dependency-injection#constructor-injection) por padrão.</span><span class="sxs-lookup"><span data-stu-id="91932-146">ASP.NET Core includes a native Inversion of Control (IoC) container that supports [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) by default.</span></span> <span data-ttu-id="91932-147">É possível substituir o contêiner padrão, se desejado.</span><span class="sxs-lookup"><span data-stu-id="91932-147">You can replace the default container if you wish.</span></span> <span data-ttu-id="91932-148">Além do [benefício de seu acoplamento flexível](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation), a DI disponibiliza serviços, tais como [registro em log](xref:fundamentals/logging/index), por todo o seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="91932-148">In addition to its [loose coupling benefit](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation), DI makes services, such as [logging](xref:fundamentals/logging/index), available throughout your app.</span></span>

<span data-ttu-id="91932-149">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="91932-149">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="91932-150">Middleware</span><span class="sxs-lookup"><span data-stu-id="91932-150">Middleware</span></span>

<span data-ttu-id="91932-151">No ASP.NET Core, você compõe o pipeline de solicitação usando o [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="91932-151">In ASP.NET Core, you compose your request pipeline using [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="91932-152">O middleware do ASP.NET Core executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="91932-152">ASP.NET Core middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="91932-153">Por convenção, um componente de middleware chamado "XYZ" é adicionado ao pipeline invocando-se um método de extensão `UseXYZ` no método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="91932-153">By convention, a middleware component called "XYZ" is added to the pipeline by invoking a `UseXYZ` extension method in the `Configure` method.</span></span>

<span data-ttu-id="91932-154">O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever seu próprio middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="91932-154">ASP.NET Core includes a rich set of built-in middleware, and you can write your own custom middleware.</span></span> <span data-ttu-id="91932-155">A [OWIN (Open Web Interface para .NET)](xref:fundamentals/owin), que permite que aplicativos Web sejam desacoplados de servidores Web, é compatível com aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91932-155">[Open Web Interface for .NET (OWIN)](xref:fundamentals/owin), which allows web apps to be decoupled from web servers, is supported in ASP.NET Core apps.</span></span>

<span data-ttu-id="91932-156">Para obter mais informações, consulte <xref:fundamentals/middleware/index> e <xref:fundamentals/owin>.</span><span class="sxs-lookup"><span data-stu-id="91932-156">For more information, see <xref:fundamentals/middleware/index> and <xref:fundamentals/owin>.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="initiate-http-requests"></a><span data-ttu-id="91932-157">Iniciar solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="91932-157">Initiate HTTP requests</span></span>

<span data-ttu-id="91932-158">O <xref:System.Net.Http.IHttpClientFactory> está disponível para acessar instâncias de <xref:System.Net.Http.HttpClient> para fazer solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="91932-158"><xref:System.Net.Http.IHttpClientFactory> is available to access <xref:System.Net.Http.HttpClient> instances to make HTTP requests.</span></span>

<span data-ttu-id="91932-159">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="91932-159">For more information, see <xref:fundamentals/http-requests>.</span></span>

::: moniker-end

## <a name="environments"></a><span data-ttu-id="91932-160">Ambientes</span><span class="sxs-lookup"><span data-stu-id="91932-160">Environments</span></span>

<span data-ttu-id="91932-161">Os ambientes, como *Desenvolvimento* e *Produção*, são uma noção de primeira classe no ASP.NET Core e podem ser definidos usando uma variável de ambiente, um arquivo de configurações e um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="91932-161">Environments, such as *Development* and *Production*, are a first-class notion in ASP.NET Core and can be set using an environment variable, settings file, and command-line argument.</span></span>

<span data-ttu-id="91932-162">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="91932-162">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="hosting"></a><span data-ttu-id="91932-163">Hospedagem</span><span class="sxs-lookup"><span data-stu-id="91932-163">Hosting</span></span>

<span data-ttu-id="91932-164">Os aplicativos ASP.NET Core configuram e iniciam um *host*, que é responsável pelo gerenciamento de inicialização e de tempo de vida do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="91932-164">ASP.NET Core apps configure and launch a *host*, which is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="91932-165">Para obter mais informações, consulte <xref:fundamentals/host/index>.</span><span class="sxs-lookup"><span data-stu-id="91932-165">For more information, see <xref:fundamentals/host/index>.</span></span>

## <a name="servers"></a><span data-ttu-id="91932-166">Servidores</span><span class="sxs-lookup"><span data-stu-id="91932-166">Servers</span></span>

<span data-ttu-id="91932-167">O modelo de hospedagem do ASP.NET Core não escuta diretamente as solicitações.</span><span class="sxs-lookup"><span data-stu-id="91932-167">The ASP.NET Core hosting model doesn't directly listen for requests.</span></span> <span data-ttu-id="91932-168">O modelo de host se baseia em uma implementação do servidor HTTP para encaminhar a solicitação ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="91932-168">The hosting model relies on an HTTP server implementation to forward the request to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="91932-169">Windows</span><span class="sxs-lookup"><span data-stu-id="91932-169">Windows</span></span>](#tab/windows)

<span data-ttu-id="91932-170">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="91932-170">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="91932-171">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web gerenciado multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="91932-171">[Kestrel](xref:fundamentals/servers/kestrel) server is a managed, cross-platform web server.</span></span> <span data-ttu-id="91932-172">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="91932-172">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="91932-173">O Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet no ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="91932-173">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="91932-174">O servidor HTTP do IIS (`IISHttpServer`) é um [servidor em processo](xref:fundamentals/servers/index#in-process-hosting-model) do IIS.</span><span class="sxs-lookup"><span data-stu-id="91932-174">IIS HTTP Server (`IISHttpServer`) is an [in-process server](xref:fundamentals/servers/index#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="91932-175">O servidor [HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor Web do ASP.NET Core no Windows.</span><span class="sxs-lookup"><span data-stu-id="91932-175">[HTTP.sys](xref:fundamentals/servers/httpsys) server is a web server for ASP.NET Core on Windows.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="91932-176">macOS</span><span class="sxs-lookup"><span data-stu-id="91932-176">macOS</span></span>](#tab/macos)

<span data-ttu-id="91932-177">O ASP.NET Core usa a implementação do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="91932-177">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="91932-178">O Kestrel é um servidor Web gerenciado multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="91932-178">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="91932-179">O Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet no ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="91932-179">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="91932-180">Linux</span><span class="sxs-lookup"><span data-stu-id="91932-180">Linux</span></span>](#tab/linux)

<span data-ttu-id="91932-181">O ASP.NET Core usa a implementação do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="91932-181">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="91932-182">O Kestrel é um servidor Web gerenciado multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="91932-182">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="91932-183">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](http://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="91932-183">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="91932-184">O Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet no ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="91932-184">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="91932-185">Windows</span><span class="sxs-lookup"><span data-stu-id="91932-185">Windows</span></span>](#tab/windows)

<span data-ttu-id="91932-186">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="91932-186">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="91932-187">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é um servidor Web gerenciado multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="91932-187">[Kestrel](xref:fundamentals/servers/kestrel) server is a managed, cross-platform web server.</span></span> <span data-ttu-id="91932-188">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="91932-188">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="91932-189">O Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet no ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="91932-189">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="91932-190">O servidor [HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor Web do ASP.NET Core no Windows.</span><span class="sxs-lookup"><span data-stu-id="91932-190">[HTTP.sys](xref:fundamentals/servers/httpsys) server is a web server for ASP.NET Core on Windows.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="91932-191">macOS</span><span class="sxs-lookup"><span data-stu-id="91932-191">macOS</span></span>](#tab/macos)

<span data-ttu-id="91932-192">O ASP.NET Core usa a implementação do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="91932-192">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="91932-193">O Kestrel é um servidor Web gerenciado multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="91932-193">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="91932-194">O Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet no ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="91932-194">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="91932-195">Linux</span><span class="sxs-lookup"><span data-stu-id="91932-195">Linux</span></span>](#tab/linux)

<span data-ttu-id="91932-196">O ASP.NET Core usa a implementação do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="91932-196">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="91932-197">O Kestrel é um servidor Web gerenciado multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="91932-197">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="91932-198">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](http://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="91932-198">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="91932-199">O Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet no ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="91932-199">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

---

::: moniker-end

<span data-ttu-id="91932-200">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="91932-200">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="91932-201">Configuração</span><span class="sxs-lookup"><span data-stu-id="91932-201">Configuration</span></span>

<span data-ttu-id="91932-202">O ASP.NET Core usa um modelo de configuração com base nos pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="91932-202">ASP.NET Core uses a configuration model based on name-value pairs.</span></span> <span data-ttu-id="91932-203">O modelo de configuração não baseado em <xref:System.Configuration> ou em *web.config*. A configuração obtém as definições de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="91932-203">The configuration model isn't based on <xref:System.Configuration> or *web.config*. Configuration obtains settings from an ordered set of configuration providers.</span></span> <span data-ttu-id="91932-204">Os provedores internos de configuração dão suporte a uma variedade de formatos de arquivo (XML, JSON, INI), variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="91932-204">The built-in configuration providers support a variety of file formats (XML, JSON, INI), environment variables, and command-line arguments.</span></span> <span data-ttu-id="91932-205">Você também pode escrever seus próprios provedores de configuração personalizados.</span><span class="sxs-lookup"><span data-stu-id="91932-205">You can also write your own custom configuration providers.</span></span>

<span data-ttu-id="91932-206">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="91932-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="logging"></a><span data-ttu-id="91932-207">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="91932-207">Logging</span></span>

<span data-ttu-id="91932-208">O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma variedade de provedores de logs.</span><span class="sxs-lookup"><span data-stu-id="91932-208">ASP.NET Core supports a Logging API that works with a variety of logging providers.</span></span> <span data-ttu-id="91932-209">Os provedores internos dão suporte ao envio de logs para um ou mais destinos.</span><span class="sxs-lookup"><span data-stu-id="91932-209">Built-in providers support sending logs to one or more destinations.</span></span> <span data-ttu-id="91932-210">As estruturas de registro em log de terceiros podem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="91932-210">Third-party logging frameworks can be used.</span></span>

<span data-ttu-id="91932-211">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="91932-211">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="91932-212">Tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="91932-212">Error handling</span></span>

<span data-ttu-id="91932-213">O ASP.NET Core tem cenários internos para tratamento de erros em aplicativos, incluindo uma página de exceção de desenvolvedor, páginas de erro personalizadas, páginas de código de status estático e tratamento de exceções de inicialização.</span><span class="sxs-lookup"><span data-stu-id="91932-213">ASP.NET Core has built-in scenarios for handling errors in apps, including a developer exception page, custom error pages, static status code pages, and startup exception handling.</span></span>

<span data-ttu-id="91932-214">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="91932-214">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="routing"></a><span data-ttu-id="91932-215">Roteamento</span><span class="sxs-lookup"><span data-stu-id="91932-215">Routing</span></span>

<span data-ttu-id="91932-216">O ASP.NET Core oferece cenários para roteamento de solicitações de aplicativo para manipuladores de rotas.</span><span class="sxs-lookup"><span data-stu-id="91932-216">ASP.NET Core offers scenarios for routing of app requests to route handlers.</span></span>

<span data-ttu-id="91932-217">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="91932-217">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="background-tasks"></a><span data-ttu-id="91932-218">Tarefas em segundo plano</span><span class="sxs-lookup"><span data-stu-id="91932-218">Background tasks</span></span>

<span data-ttu-id="91932-219">As tarefas em segundo plano são implementadas como *serviços hospedados*.</span><span class="sxs-lookup"><span data-stu-id="91932-219">Background tasks are implemented as *hosted services*.</span></span> <span data-ttu-id="91932-220">Um serviço hospedado é uma classe com lógica de tarefa em segundo plano que implementa a interface <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="91932-220">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="91932-221">Para obter mais informações, consulte <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="91932-221">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="access-httpcontext"></a><span data-ttu-id="91932-222">Acessar o HttpContext</span><span class="sxs-lookup"><span data-stu-id="91932-222">Access HttpContext</span></span>

<span data-ttu-id="91932-223">`HttpContext` está disponível automaticamente durante o processamento de solicitações com Razor Pages e com o MVC.</span><span class="sxs-lookup"><span data-stu-id="91932-223">`HttpContext` is automatically available when processing requests with Razor Pages and MVC.</span></span> <span data-ttu-id="91932-224">Em circunstâncias em que `HttpContext` não está prontamente disponível, você pode acessar o `HttpContext` por meio da interface <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> e sua implementação padrão, <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span><span class="sxs-lookup"><span data-stu-id="91932-224">In circumstances where `HttpContext` isn't readily available, you can access the `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation, <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span>

<span data-ttu-id="91932-225">Para obter mais informações, consulte <xref:fundamentals/httpcontext>.</span><span class="sxs-lookup"><span data-stu-id="91932-225">For more information, see <xref:fundamentals/httpcontext>.</span></span>
