---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2019
uid: fundamentals/index
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="e2417-103">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e2417-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="e2417-104">Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2417-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="e2417-105">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="e2417-105">The Startup class</span></span>

<span data-ttu-id="e2417-106">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="e2417-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="e2417-107">Quaisquer serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="e2417-107">Any services required by the app are configured.</span></span>
* <span data-ttu-id="e2417-108">O pipeline de tratamento de solicitação é definido.</span><span class="sxs-lookup"><span data-stu-id="e2417-108">The request handling pipeline is defined.</span></span>

* <span data-ttu-id="e2417-109">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e2417-109">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2417-110">*Serviços* são componentes usados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2417-110">*Services* are components that are used by the app.</span></span> <span data-ttu-id="e2417-111">Por exemplo, um objeto de contexto do Entity Framework Core é um serviço.</span><span class="sxs-lookup"><span data-stu-id="e2417-111">For example, an Entity Framework Core context object is a service.</span></span>
* <span data-ttu-id="e2417-112">O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e2417-112">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span> <span data-ttu-id="e2417-113">O pipeline é composto como uma série de componentes de *middleware*.</span><span class="sxs-lookup"><span data-stu-id="e2417-113">The pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="e2417-114">Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e2417-114">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="e2417-115">Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="e2417-115">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e2417-116">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="e2417-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

::: moniker-end

<span data-ttu-id="e2417-117">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="e2417-117">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="e2417-118">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="e2417-118">Dependency injection (services)</span></span>

<span data-ttu-id="e2417-119">O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2417-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="e2417-120">Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário.</span><span class="sxs-lookup"><span data-stu-id="e2417-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="e2417-121">O parâmetro pode ser o tipo de serviço ou uma interface.</span><span class="sxs-lookup"><span data-stu-id="e2417-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="e2417-122">O sistema de DI fornece o serviço em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="e2417-122">The DI system provides the service at runtime.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e2417-123">Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="e2417-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="e2417-124">A linha realçada é um exemplo de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="e2417-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="e2417-125">Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.</span><span class="sxs-lookup"><span data-stu-id="e2417-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="e2417-126">Para obter mais informações, consulte [Injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2417-126">For more information, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware"></a><span data-ttu-id="e2417-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="e2417-127">Middleware</span></span>

<span data-ttu-id="e2417-128">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="e2417-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="e2417-129">Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="e2417-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="e2417-130">Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e2417-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="e2417-131">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="e2417-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e2417-132">O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="e2417-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

::: moniker-end

<span data-ttu-id="e2417-133">O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="e2417-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="e2417-134">Para obter mais informações, veja [Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="e2417-134">For more information, see [Middleware](xref:fundamentals/middleware/index).</span></span>

<a id="host"/>

## <a name="the-host"></a><span data-ttu-id="e2417-135">O host</span><span class="sxs-lookup"><span data-stu-id="e2417-135">The host</span></span>

<span data-ttu-id="e2417-136">Um aplicativo ASP.NET Core cria um *host* na inicialização.</span><span class="sxs-lookup"><span data-stu-id="e2417-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="e2417-137">O host é um objeto que encapsula todos os recursos do aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="e2417-137">The host is an object that encapsulates all of the the app's resources, such as:</span></span>

* <span data-ttu-id="e2417-138">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="e2417-138">An HTTP server implementation</span></span>
* <span data-ttu-id="e2417-139">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="e2417-139">Middleware components</span></span>
* <span data-ttu-id="e2417-140">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="e2417-140">Logging</span></span>
* <span data-ttu-id="e2417-141">DI</span><span class="sxs-lookup"><span data-stu-id="e2417-141">DI</span></span>
* <span data-ttu-id="e2417-142">Configuração</span><span class="sxs-lookup"><span data-stu-id="e2417-142">Configuration</span></span>

<span data-ttu-id="e2417-143">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="e2417-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="e2417-144">O código para criar um host está em `Program.Main` e segue o [padrão de construtor](https://wikipedia.org/wiki/Builder_pattern).</span><span class="sxs-lookup"><span data-stu-id="e2417-144">The code to create a host is in `Program.Main` and follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern).</span></span> <span data-ttu-id="e2417-145">Métodos são chamados para configurar cada recurso que faz parte do host.</span><span class="sxs-lookup"><span data-stu-id="e2417-145">Methods are called to configure each resource that is part of the host.</span></span> <span data-ttu-id="e2417-146">Um método construtor é chamado para reunir tudo e instanciar o objeto de host.</span><span class="sxs-lookup"><span data-stu-id="e2417-146">A builder method is called to pull it all together and instantiate the host object.</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e2417-147">O ASP.NET Core 2.x usa o Host da Web (a classe `WebHost`) para aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="e2417-147">ASP.NET Core 2.x uses Web Host (the `WebHost` class) for web apps.</span></span> <span data-ttu-id="e2417-148">A estrutura fornece `CreateDefaultBuilder` para configurar um host com opções comumente usadas, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="e2417-148">The framework provides `CreateDefaultBuilder` to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="e2417-149">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="e2417-149">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="e2417-150">Carregamento da configuração de *appsettings.json*, de variáveis de ambiente, de argumentos de linha de comando e de outras fontes.</span><span class="sxs-lookup"><span data-stu-id="e2417-150">Load configuration from *appsettings.json*, environment variables, command line arguments, and other sources.</span></span>
* <span data-ttu-id="e2417-151">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="e2417-151">Send logging output to the console and debug providers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="e2417-152">Aqui está um exemplo de código que cria um host:</span><span class="sxs-lookup"><span data-stu-id="e2417-152">Here's sample code that builds a host:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

<span data-ttu-id="e2417-153">Para obter mais informações, confira [Host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="e2417-153">For more information, see [Web Host](xref:fundamentals/host/web-host).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="e2417-154">No ASP.NET Core 3.0, o Host da Web (classe `WebHost`) ou o Host Genérico (classe `Host`) podem ser usados em um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="e2417-154">In ASP.NET Core 3.0, Web Host (`WebHost` class) or Generic Host (`Host` class) can be used in a web app.</span></span> <span data-ttu-id="e2417-155">O Host Genérico é o recomendado, e o Host da Web está disponível para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="e2417-155">Generic Host is recommended, and Web Host is available for backwards compatibility.</span></span>

<span data-ttu-id="e2417-156">A estrutura fornece os métodos `CreateDefaultBuilder` e `ConfigureWebHostDefaults` para configurar um host com opções comumente usadas, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="e2417-156">The framework provides the `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="e2417-157">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="e2417-157">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="e2417-158">Carregamento da configuração de *appsettings.json*, *appsettings.[EnvironmentName].json*, de variáveis de ambiente e de argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="e2417-158">Load configuration from *appsettings.json*, *appsettings.[EnvironmentName].json*, environment variables, and command line arguments.</span></span>
* <span data-ttu-id="e2417-159">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="e2417-159">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="e2417-160">Aqui está um exemplo de código que cria um host.</span><span class="sxs-lookup"><span data-stu-id="e2417-160">Here's sample code that builds a host.</span></span> <span data-ttu-id="e2417-161">Os métodos que configuram o host com as opções mais usadas são realçados.</span><span class="sxs-lookup"><span data-stu-id="e2417-161">The methods that set up the host with commonly used options are highlighted.</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

<span data-ttu-id="e2417-162">Para obter mais informações, veja [Host Genérico](xref:fundamentals/host/generic-host) e [Host da Web](xref:fundamentals/host/web-host)</span><span class="sxs-lookup"><span data-stu-id="e2417-162">For more information, see [Generic Host](xref:fundamentals/host/generic-host) and [Web Host](xref:fundamentals/host/web-host)</span></span>

::: moniker-end

### <a name="advanced-host-scenarios"></a><span data-ttu-id="e2417-163">Cenários avançados de host</span><span class="sxs-lookup"><span data-stu-id="e2417-163">Advanced host scenarios</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="e2417-164">O Host da Web foi projetado para incluir uma implementação de servidor HTTP, que não é necessária para outros tipos de aplicativos .NET.</span><span class="sxs-lookup"><span data-stu-id="e2417-164">Web Host is designed to include an HTTP server implementation, which isn't needed for other kinds of .NET apps.</span></span> <span data-ttu-id="e2417-165">A partir da versão 2.1, o Host Genérico (classe `Host`) está disponível para uso por qualquer aplicativo .NET Core, não apenas para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2417-165">Starting in 2.1, Generic Host (`Host` class) is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="e2417-166">O Host Genérico permite que você use funcionalidades abrangentes como registro em log, DI, configuração e gerenciamento de tempo de vida de aplicativo em outros tipos de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="e2417-166">Generic Host lets you use cross-cutting features such as logging, DI, configuration, and app lifetime management in other types of apps.</span></span> <span data-ttu-id="e2417-167">Para obter mais informações, veja [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e2417-167">For more information, see [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="e2417-168">O Host Genérico está disponível para uso por qualquer aplicativo .NET Core, não apenas para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2417-168">Generic Host is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="e2417-169">O Host Genérico permite que você use funcionalidades abrangentes como registro em log, DI, configuração e gerenciamento de tempo de vida de aplicativo em outros tipos de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="e2417-169">Generic Host lets you use cross-cutting features such as logging, DI, configuration, and app lifetime management in other types of apps.</span></span> <span data-ttu-id="e2417-170">Para obter mais informações, veja [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e2417-170">For more information, see [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

<span data-ttu-id="e2417-171">Você também pode usar o host para executar tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="e2417-171">You can also use the host to run background tasks.</span></span> <span data-ttu-id="e2417-172">Para obter mais informações, veja [Tarefas em segundo plano](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="e2417-172">For more information, see [Background tasks](xref:fundamentals/host/hosted-services).</span></span>

## <a name="servers"></a><span data-ttu-id="e2417-173">Servidores</span><span class="sxs-lookup"><span data-stu-id="e2417-173">Servers</span></span>

<span data-ttu-id="e2417-174">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="e2417-174">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="e2417-175">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="e2417-175">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="e2417-176">Windows</span><span class="sxs-lookup"><span data-stu-id="e2417-176">Windows</span></span>](#tab/windows)

<span data-ttu-id="e2417-177">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="e2417-177">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="e2417-178">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e2417-178">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="e2417-179">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="e2417-179">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="e2417-180">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="e2417-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="e2417-181">O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS.</span><span class="sxs-lookup"><span data-stu-id="e2417-181">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="e2417-182">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="e2417-182">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="e2417-183">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="e2417-183">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="e2417-184">macOS</span><span class="sxs-lookup"><span data-stu-id="e2417-184">macOS</span></span>](#tab/macos)

<span data-ttu-id="e2417-185">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="e2417-185">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="e2417-186">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="e2417-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="e2417-187">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="e2417-187">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="e2417-188">Linux</span><span class="sxs-lookup"><span data-stu-id="e2417-188">Linux</span></span>](#tab/linux)

<span data-ttu-id="e2417-189">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="e2417-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="e2417-190">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="e2417-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="e2417-191">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="e2417-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="e2417-192">Windows</span><span class="sxs-lookup"><span data-stu-id="e2417-192">Windows</span></span>](#tab/windows)

<span data-ttu-id="e2417-193">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="e2417-193">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="e2417-194">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e2417-194">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="e2417-195">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="e2417-195">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="e2417-196">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="e2417-196">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="e2417-197">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="e2417-197">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="e2417-198">macOS</span><span class="sxs-lookup"><span data-stu-id="e2417-198">macOS</span></span>](#tab/macos)

<span data-ttu-id="e2417-199">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="e2417-199">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="e2417-200">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="e2417-200">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="e2417-201">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="e2417-201">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="e2417-202">Linux</span><span class="sxs-lookup"><span data-stu-id="e2417-202">Linux</span></span>](#tab/linux)

<span data-ttu-id="e2417-203">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="e2417-203">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="e2417-204">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="e2417-204">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="e2417-205">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](http://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="e2417-205">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="e2417-206">Para obter mais informações, veja [Servidores](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="e2417-206">For more information, see [Servers](xref:fundamentals/servers/index).</span></span>

## <a name="configuration"></a><span data-ttu-id="e2417-207">Configuração</span><span class="sxs-lookup"><span data-stu-id="e2417-207">Configuration</span></span>

<span data-ttu-id="e2417-208">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="e2417-208">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="e2417-209">Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="e2417-209">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="e2417-210">Você também pode escrever seus próprios provedores de configuração personalizados.</span><span class="sxs-lookup"><span data-stu-id="e2417-210">You can also write custom configuration providers.</span></span>

<span data-ttu-id="e2417-211">Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e2417-211">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="e2417-212">Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e2417-212">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="e2417-213">Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.</span><span class="sxs-lookup"><span data-stu-id="e2417-213">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="e2417-214">Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e2417-214">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e2417-215">Para segredos de produção, recomendamos o [Azure Key Vault](/aspnet/core/security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="e2417-215">For production secrets, we recommend [Azure Key Vault](/aspnet/core/security/key-vault-configuration).</span></span>

<span data-ttu-id="e2417-216">Para obter mais informações, consulte [Configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e2417-216">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="options"></a><span data-ttu-id="e2417-217">Opções</span><span class="sxs-lookup"><span data-stu-id="e2417-217">Options</span></span>

<span data-ttu-id="e2417-218">Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="e2417-218">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="e2417-219">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e2417-219">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="e2417-220">Por exemplo, o código a seguir define as opções de WebSockets:</span><span class="sxs-lookup"><span data-stu-id="e2417-220">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="e2417-221">Para obter mais informações, veja [Opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="e2417-221">For more information, see [Options](xref:fundamentals/configuration/options).</span></span>

## <a name="environments"></a><span data-ttu-id="e2417-222">Ambientes</span><span class="sxs-lookup"><span data-stu-id="e2417-222">Environments</span></span>

<span data-ttu-id="e2417-223">Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2417-223">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="e2417-224">Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="e2417-224">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="e2417-225">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="e2417-225">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="e2417-226">O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="e2417-226">The environment object is available anywhere in the app via DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e2417-227">O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="e2417-227">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

::: moniker-end

<span data-ttu-id="e2417-228">Para obter mais informações, veja [Ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="e2417-228">For more information, see [Environments](xref:fundamentals/environments).</span></span>

## <a name="logging"></a><span data-ttu-id="e2417-229">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="e2417-229">Logging</span></span>

<span data-ttu-id="e2417-230">O ASP.NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="e2417-230">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="e2417-231">Provedores disponíveis incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="e2417-231">Available providers include the following:</span></span>

* <span data-ttu-id="e2417-232">Console</span><span class="sxs-lookup"><span data-stu-id="e2417-232">Console</span></span>
* <span data-ttu-id="e2417-233">Depurar</span><span class="sxs-lookup"><span data-stu-id="e2417-233">Debug</span></span>
* <span data-ttu-id="e2417-234">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="e2417-234">Event Tracing on Windows</span></span>
* <span data-ttu-id="e2417-235">Log de Eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="e2417-235">Windows Event Log</span></span>
* <span data-ttu-id="e2417-236">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e2417-236">TraceSource</span></span>
* <span data-ttu-id="e2417-237">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e2417-237">Azure App Service</span></span>
* <span data-ttu-id="e2417-238">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="e2417-238">Azure Application Insights</span></span>

<span data-ttu-id="e2417-239">Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.</span><span class="sxs-lookup"><span data-stu-id="e2417-239">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e2417-240">Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.</span><span class="sxs-lookup"><span data-stu-id="e2417-240">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

::: moniker-end

<span data-ttu-id="e2417-241">A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log.</span><span class="sxs-lookup"><span data-stu-id="e2417-241">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="e2417-242">Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="e2417-242">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="e2417-243">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="e2417-243">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="e2417-244">Para obter mais informações, veja [Registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e2417-244">For more information, see [Logging](xref:fundamentals/logging/index).</span></span>

## <a name="routing"></a><span data-ttu-id="e2417-245">Roteamento</span><span class="sxs-lookup"><span data-stu-id="e2417-245">Routing</span></span>

<span data-ttu-id="e2417-246">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="e2417-246">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="e2417-247">O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="e2417-247">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="e2417-248">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2417-248">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="e2417-249">Para obter mais informações, consulte [Roteamento](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="e2417-249">For more information, see [Routing](xref:fundamentals/routing).</span></span>

## <a name="error-handling"></a><span data-ttu-id="e2417-250">Tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="e2417-250">Error handling</span></span>

<span data-ttu-id="e2417-251">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="e2417-251">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="e2417-252">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="e2417-252">A developer exception page</span></span>
* <span data-ttu-id="e2417-253">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="e2417-253">Custom error pages</span></span>
* <span data-ttu-id="e2417-254">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="e2417-254">Static status code pages</span></span>
* <span data-ttu-id="e2417-255">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="e2417-255">Startup exception handling</span></span>

<span data-ttu-id="e2417-256">Para obter mais informações, veja [Tratamento de erro](xref:fundamentals/error-handling).</span><span class="sxs-lookup"><span data-stu-id="e2417-256">For more information, see [Error handling](xref:fundamentals/error-handling).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="make-http-requests"></a><span data-ttu-id="e2417-257">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="e2417-257">Make HTTP requests</span></span>

<span data-ttu-id="e2417-258">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e2417-258">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="e2417-259">O alocador:</span><span class="sxs-lookup"><span data-stu-id="e2417-259">The factory:</span></span>

* <span data-ttu-id="e2417-260">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e2417-260">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="e2417-261">Por exemplo, um cliente *github* pode ser registrado e configurado para acessar o GitHub.</span><span class="sxs-lookup"><span data-stu-id="e2417-261">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="e2417-262">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="e2417-262">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="e2417-263">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="e2417-263">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="e2417-264">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2417-264">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="e2417-265">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="e2417-265">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="e2417-266">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="e2417-266">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="e2417-267">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="e2417-267">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="e2417-268">Adiciona uma experiência de registro em log configurável (via *ILogger*) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="e2417-268">Adds a configurable logging experience (via *ILogger*) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="e2417-269">Para obter mais informações, veja [Fazer solicitações HTTP](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="e2417-269">For more information, see [Make HTTP requests](xref:fundamentals/http-requests).</span></span>

::: moniker-end

## <a name="content-root"></a><span data-ttu-id="e2417-270">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="e2417-270">Content root</span></span>

<span data-ttu-id="e2417-271">A raiz do conteúdo é o caminho base para qualquer conteúdo privado usado pelo aplicativo, como seus arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="e2417-271">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="e2417-272">Por padrão, a raiz do conteúdo é o caminho base do executável que hospeda o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e2417-272">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="e2417-273">Um local alternativo pode ser especificado ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="e2417-273">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e2417-274">Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/web-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="e2417-274">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="e2417-275">Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/generic-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="e2417-275">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="e2417-276">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="e2417-276">Web root</span></span>

<span data-ttu-id="e2417-277">A raiz Web (também conhecida como *webroot*) é o caminho base para recursos públicos e estáticos como CSS, JavaScript e arquivos de imagem.</span><span class="sxs-lookup"><span data-stu-id="e2417-277">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="e2417-278">O middleware de arquivos estáticos apenas veiculará arquivos do diretório raiz Web (e seus subdiretórios) por padrão.</span><span class="sxs-lookup"><span data-stu-id="e2417-278">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="e2417-279">O caminho da raiz Web assume como padrão *\<raiz do conteúdo>/wwwroot*, mas é possível especificar um local diferente ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="e2417-279">The web root path defaults to *\<content root>/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

<span data-ttu-id="e2417-280">Em arquivos (*.cshtml*) do Razor, o til-barra `~/` aponta para a raiz Web.</span><span class="sxs-lookup"><span data-stu-id="e2417-280">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="e2417-281">Caminhos que começam com `~/` são denominados caminhos virtuais.</span><span class="sxs-lookup"><span data-stu-id="e2417-281">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="e2417-282">Para obter mais informações, veja [Arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="e2417-282">For more information, see [Static files](xref:fundamentals/static-files).</span></span>
