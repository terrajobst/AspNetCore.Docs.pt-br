---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: fundamentals/index
ms.openlocfilehash: cff2afd62ed60648dc689d408dde56ecda18c261
ms.sourcegitcommit: 2d4c1732c4866ed26b83da35f7bc2ad021a9c701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815645"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="a9436-103">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9436-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="a9436-104">Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9436-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="a9436-105">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="a9436-105">The Startup class</span></span>

<span data-ttu-id="a9436-106">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="a9436-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="a9436-107">Os serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="a9436-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="a9436-108">O pipeline de tratamento de solicitação é definido.</span><span class="sxs-lookup"><span data-stu-id="a9436-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="a9436-109">*Serviços* são componentes usados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9436-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="a9436-110">Por exemplo, um componente de registro em log é um serviço.</span><span class="sxs-lookup"><span data-stu-id="a9436-110">For example, a logging component is a service.</span></span> <span data-ttu-id="a9436-111">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a9436-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="a9436-112">O pipeline de tratamento de solicitações é composto como uma série de componentes de *middleware*.</span><span class="sxs-lookup"><span data-stu-id="a9436-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="a9436-113">Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a9436-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="a9436-114">Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a9436-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="a9436-115">O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a9436-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="a9436-116">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="a9436-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="a9436-117">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a9436-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="a9436-118">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="a9436-118">Dependency injection (services)</span></span>

<span data-ttu-id="a9436-119">O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9436-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="a9436-120">Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário.</span><span class="sxs-lookup"><span data-stu-id="a9436-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="a9436-121">O parâmetro pode ser o tipo de serviço ou uma interface.</span><span class="sxs-lookup"><span data-stu-id="a9436-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="a9436-122">O sistema de DI fornece o serviço em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="a9436-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="a9436-123">Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="a9436-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="a9436-124">A linha realçada é um exemplo de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="a9436-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="a9436-125">Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.</span><span class="sxs-lookup"><span data-stu-id="a9436-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="a9436-126">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a9436-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="a9436-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="a9436-127">Middleware</span></span>

<span data-ttu-id="a9436-128">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="a9436-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="a9436-129">Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a9436-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="a9436-130">Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a9436-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="a9436-131">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="a9436-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="a9436-132">O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="a9436-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="a9436-133">O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="a9436-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="a9436-134">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="a9436-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="a9436-135">Host</span><span class="sxs-lookup"><span data-stu-id="a9436-135">Host</span></span>

<span data-ttu-id="a9436-136">Um aplicativo ASP.NET Core cria um *host* na inicialização.</span><span class="sxs-lookup"><span data-stu-id="a9436-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="a9436-137">O host é um objeto que encapsula todos os recursos do aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="a9436-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="a9436-138">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="a9436-138">An HTTP server implementation</span></span>
* <span data-ttu-id="a9436-139">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="a9436-139">Middleware components</span></span>
* <span data-ttu-id="a9436-140">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="a9436-140">Logging</span></span>
* <span data-ttu-id="a9436-141">DI</span><span class="sxs-lookup"><span data-stu-id="a9436-141">DI</span></span>
* <span data-ttu-id="a9436-142">Configuração</span><span class="sxs-lookup"><span data-stu-id="a9436-142">Configuration</span></span>

<span data-ttu-id="a9436-143">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="a9436-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9436-144">Dois hosts estão disponíveis: o Host Genérico e o Host Web.</span><span class="sxs-lookup"><span data-stu-id="a9436-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="a9436-145">O Host Genérico é o recomendado, e o Host Web está disponível apenas para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="a9436-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="a9436-146">O código para criar um host está em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a9436-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="a9436-147">Os métodos `CreateDefaultBuilder` e `ConfigureWebHostDefaults` configuram um host com as opções usadas com frequência, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="a9436-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="a9436-148">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="a9436-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="a9436-149">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="a9436-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="a9436-150">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="a9436-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="a9436-151">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="a9436-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9436-152">Dois hosts estão disponíveis: o Host Web e o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="a9436-152">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="a9436-153">No ASP.NET Core 2.x, o Host Genérico é somente para cenários não Web.</span><span class="sxs-lookup"><span data-stu-id="a9436-153">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="a9436-154">O código para criar um host está em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a9436-154">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="a9436-155">O método `CreateDefaultBuilder` configura um host com as opções usadas com frequência, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="a9436-155">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="a9436-156">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="a9436-156">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="a9436-157">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="a9436-157">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="a9436-158">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="a9436-158">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="a9436-159">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="a9436-159">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="non-web-scenarios"></a><span data-ttu-id="a9436-160">Cenários não Web</span><span class="sxs-lookup"><span data-stu-id="a9436-160">Non-web scenarios</span></span>

<span data-ttu-id="a9436-161">O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a9436-161">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="a9436-162">Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="a9436-162">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="a9436-163">Servidores</span><span class="sxs-lookup"><span data-stu-id="a9436-163">Servers</span></span>

<span data-ttu-id="a9436-164">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="a9436-164">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="a9436-165">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="a9436-165">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="a9436-166">Windows</span><span class="sxs-lookup"><span data-stu-id="a9436-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="a9436-167">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="a9436-167">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="a9436-168">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="a9436-168">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="a9436-169">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="a9436-169">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="a9436-170">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="a9436-170">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="a9436-171">O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS.</span><span class="sxs-lookup"><span data-stu-id="a9436-171">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="a9436-172">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="a9436-172">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="a9436-173">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="a9436-173">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="a9436-174">macOS</span><span class="sxs-lookup"><span data-stu-id="a9436-174">macOS</span></span>](#tab/macos)

<span data-ttu-id="a9436-175">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="a9436-175">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a9436-176">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="a9436-176">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a9436-177">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="a9436-177">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="a9436-178">Linux</span><span class="sxs-lookup"><span data-stu-id="a9436-178">Linux</span></span>](#tab/linux)

<span data-ttu-id="a9436-179">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="a9436-179">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a9436-180">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="a9436-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a9436-181">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="a9436-181">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="a9436-182">Windows</span><span class="sxs-lookup"><span data-stu-id="a9436-182">Windows</span></span>](#tab/windows)

<span data-ttu-id="a9436-183">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="a9436-183">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="a9436-184">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="a9436-184">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="a9436-185">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="a9436-185">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="a9436-186">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="a9436-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="a9436-187">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="a9436-187">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="a9436-188">macOS</span><span class="sxs-lookup"><span data-stu-id="a9436-188">macOS</span></span>](#tab/macos)

<span data-ttu-id="a9436-189">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="a9436-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a9436-190">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="a9436-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a9436-191">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="a9436-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="a9436-192">Linux</span><span class="sxs-lookup"><span data-stu-id="a9436-192">Linux</span></span>](#tab/linux)

<span data-ttu-id="a9436-193">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="a9436-193">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a9436-194">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="a9436-194">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a9436-195">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="a9436-195">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="a9436-196">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="a9436-196">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="a9436-197">Configuração</span><span class="sxs-lookup"><span data-stu-id="a9436-197">Configuration</span></span>

<span data-ttu-id="a9436-198">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="a9436-198">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="a9436-199">Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="a9436-199">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="a9436-200">Você também pode escrever seus próprios provedores de configuração personalizados.</span><span class="sxs-lookup"><span data-stu-id="a9436-200">You can also write custom configuration providers.</span></span>

<span data-ttu-id="a9436-201">Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="a9436-201">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="a9436-202">Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a9436-202">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="a9436-203">Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.</span><span class="sxs-lookup"><span data-stu-id="a9436-203">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="a9436-204">Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a9436-204">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a9436-205">Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="a9436-205">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="a9436-206">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a9436-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="a9436-207">Opções</span><span class="sxs-lookup"><span data-stu-id="a9436-207">Options</span></span>

<span data-ttu-id="a9436-208">Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="a9436-208">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="a9436-209">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="a9436-209">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="a9436-210">Por exemplo, o código a seguir define as opções de WebSockets:</span><span class="sxs-lookup"><span data-stu-id="a9436-210">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="a9436-211">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a9436-211">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="a9436-212">Ambientes</span><span class="sxs-lookup"><span data-stu-id="a9436-212">Environments</span></span>

<span data-ttu-id="a9436-213">Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9436-213">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="a9436-214">Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a9436-214">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="a9436-215">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="a9436-215">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="a9436-216">O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="a9436-216">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="a9436-217">O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="a9436-217">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="a9436-218">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a9436-218">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="a9436-219">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="a9436-219">Logging</span></span>

<span data-ttu-id="a9436-220">O ASP.NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="a9436-220">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="a9436-221">Provedores disponíveis incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="a9436-221">Available providers include the following:</span></span>

* <span data-ttu-id="a9436-222">Console</span><span class="sxs-lookup"><span data-stu-id="a9436-222">Console</span></span>
* <span data-ttu-id="a9436-223">Depurar</span><span class="sxs-lookup"><span data-stu-id="a9436-223">Debug</span></span>
* <span data-ttu-id="a9436-224">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="a9436-224">Event Tracing on Windows</span></span>
* <span data-ttu-id="a9436-225">Log de Eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="a9436-225">Windows Event Log</span></span>
* <span data-ttu-id="a9436-226">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a9436-226">TraceSource</span></span>
* <span data-ttu-id="a9436-227">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="a9436-227">Azure App Service</span></span>
* <span data-ttu-id="a9436-228">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="a9436-228">Azure Application Insights</span></span>

<span data-ttu-id="a9436-229">Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.</span><span class="sxs-lookup"><span data-stu-id="a9436-229">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="a9436-230">Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.</span><span class="sxs-lookup"><span data-stu-id="a9436-230">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="a9436-231">A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log.</span><span class="sxs-lookup"><span data-stu-id="a9436-231">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="a9436-232">Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="a9436-232">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="a9436-233">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="a9436-233">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="a9436-234">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="a9436-234">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="a9436-235">Roteamento</span><span class="sxs-lookup"><span data-stu-id="a9436-235">Routing</span></span>

<span data-ttu-id="a9436-236">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="a9436-236">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="a9436-237">O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="a9436-237">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="a9436-238">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9436-238">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="a9436-239">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a9436-239">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="a9436-240">Tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="a9436-240">Error handling</span></span>

<span data-ttu-id="a9436-241">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="a9436-241">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="a9436-242">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="a9436-242">A developer exception page</span></span>
* <span data-ttu-id="a9436-243">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="a9436-243">Custom error pages</span></span>
* <span data-ttu-id="a9436-244">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="a9436-244">Static status code pages</span></span>
* <span data-ttu-id="a9436-245">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="a9436-245">Startup exception handling</span></span>

<span data-ttu-id="a9436-246">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="a9436-246">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="a9436-247">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="a9436-247">Make HTTP requests</span></span>

<span data-ttu-id="a9436-248">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a9436-248">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="a9436-249">O alocador:</span><span class="sxs-lookup"><span data-stu-id="a9436-249">The factory:</span></span>

* <span data-ttu-id="a9436-250">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a9436-250">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="a9436-251">Por exemplo, um cliente *github* pode ser registrado e configurado para acessar o GitHub.</span><span class="sxs-lookup"><span data-stu-id="a9436-251">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="a9436-252">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="a9436-252">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="a9436-253">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="a9436-253">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="a9436-254">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a9436-254">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="a9436-255">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="a9436-255">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="a9436-256">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="a9436-256">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="a9436-257">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a9436-257">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="a9436-258">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="a9436-258">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="a9436-259">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="a9436-259">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="a9436-260">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="a9436-260">Content root</span></span>

<span data-ttu-id="a9436-261">A raiz do conteúdo é o caminho base para qualquer conteúdo privado usado pelo aplicativo, como seus arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="a9436-261">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="a9436-262">Por padrão, a raiz do conteúdo é o caminho base do executável que hospeda o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a9436-262">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="a9436-263">Um local alternativo pode ser especificado ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="a9436-263">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9436-264">Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/generic-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="a9436-264">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9436-265">Para obter mais informações, veja [Raiz de conteúdo](xref:fundamentals/host/web-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="a9436-265">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="a9436-266">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="a9436-266">Web root</span></span>

<span data-ttu-id="a9436-267">A raiz Web (também conhecida como *webroot*) é o caminho base para recursos públicos e estáticos como CSS, JavaScript e arquivos de imagem.</span><span class="sxs-lookup"><span data-stu-id="a9436-267">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="a9436-268">O middleware de arquivos estáticos apenas veiculará arquivos do diretório raiz Web (e seus subdiretórios) por padrão.</span><span class="sxs-lookup"><span data-stu-id="a9436-268">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="a9436-269">O caminho da raiz Web assume como padrão *{Content Root}/wwwroot*, mas é possível especificar um local diferente ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="a9436-269">The web root path defaults to *{Content Root}/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9436-270">Para obter mais informações, consulte [Webroot](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#webroot)</span><span class="sxs-lookup"><span data-stu-id="a9436-270">For more information, see [WebRoot](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#webroot)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9436-271">Para obter mais informações, confira [Diretório base](/aspnet/core/fundamentals/host/web-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="a9436-271">For more information, see [Web root](/aspnet/core/fundamentals/host/web-host#webroot).</span></span>

::: moniker-end

<span data-ttu-id="a9436-272">Em arquivos ( *.cshtml*) do Razor, o til-barra `~/` aponta para a raiz Web.</span><span class="sxs-lookup"><span data-stu-id="a9436-272">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="a9436-273">Caminhos que começam com `~/` são denominados caminhos virtuais.</span><span class="sxs-lookup"><span data-stu-id="a9436-273">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="a9436-274">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a9436-274">For more information, see <xref:fundamentals/static-files>.</span></span>
