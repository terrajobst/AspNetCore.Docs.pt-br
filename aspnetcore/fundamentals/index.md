---
title: Conceitos básicos do ASP.NET Core
author: rick-anderson
description: Aprenda os conceitos fundamentais para a criação de aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: a16a2fbb4ad2a79f96b6646ffdc359619d361a25
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434311"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="0dde3-103">Conceitos básicos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0dde3-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0dde3-104">Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="0dde3-105">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="0dde3-105">The Startup class</span></span>

<span data-ttu-id="0dde3-106">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="0dde3-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="0dde3-107">Os serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="0dde3-108">O pipeline de tratamento de solicitação é definido.</span><span class="sxs-lookup"><span data-stu-id="0dde3-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="0dde3-109">*Serviços* são componentes usados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="0dde3-110">Por exemplo, um componente de registro em log é um serviço.</span><span class="sxs-lookup"><span data-stu-id="0dde3-110">For example, a logging component is a service.</span></span> <span data-ttu-id="0dde3-111">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="0dde3-112">O pipeline de tratamento de solicitações é composto como uma série de componentes de *middleware*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="0dde3-113">Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="0dde3-114">Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0dde3-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="0dde3-115">O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="0dde3-116">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="0dde3-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="0dde3-117">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="0dde3-118">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="0dde3-118">Dependency injection (services)</span></span>

<span data-ttu-id="0dde3-119">O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="0dde3-120">Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário.</span><span class="sxs-lookup"><span data-stu-id="0dde3-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="0dde3-121">O parâmetro pode ser o tipo de serviço ou uma interface.</span><span class="sxs-lookup"><span data-stu-id="0dde3-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="0dde3-122">O sistema de DI fornece o serviço em runtime.</span><span class="sxs-lookup"><span data-stu-id="0dde3-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="0dde3-123">Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="0dde3-124">A linha realçada é um exemplo de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="0dde3-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="0dde3-125">Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.</span><span class="sxs-lookup"><span data-stu-id="0dde3-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="0dde3-126">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="0dde3-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="0dde3-127">Middleware</span></span>

<span data-ttu-id="0dde3-128">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="0dde3-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="0dde3-129">Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0dde3-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="0dde3-130">Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="0dde3-131">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="0dde3-132">O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="0dde3-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="0dde3-133">O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="0dde3-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="0dde3-134">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="0dde3-135">Host</span><span class="sxs-lookup"><span data-stu-id="0dde3-135">Host</span></span>

<span data-ttu-id="0dde3-136">Um aplicativo ASP.NET Core cria um *host* na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0dde3-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="0dde3-137">O host é um objeto que encapsula todos os recursos do aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="0dde3-138">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="0dde3-138">An HTTP server implementation</span></span>
* <span data-ttu-id="0dde3-139">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="0dde3-139">Middleware components</span></span>
* <span data-ttu-id="0dde3-140">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="0dde3-140">Logging</span></span>
* <span data-ttu-id="0dde3-141">DI</span><span class="sxs-lookup"><span data-stu-id="0dde3-141">DI</span></span>
* <span data-ttu-id="0dde3-142">Configuração</span><span class="sxs-lookup"><span data-stu-id="0dde3-142">Configuration</span></span>

<span data-ttu-id="0dde3-143">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0dde3-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="0dde3-144">Dois hosts estão disponíveis: o Host Genérico e o Host Web.</span><span class="sxs-lookup"><span data-stu-id="0dde3-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="0dde3-145">O Host Genérico é o recomendado, e o Host Web está disponível apenas para compatibilidade com versões anteriores.</span><span class="sxs-lookup"><span data-stu-id="0dde3-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="0dde3-146">O código para criar um host está em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0dde3-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="0dde3-147">Os métodos `CreateDefaultBuilder` e `ConfigureWebHostDefaults` configuram um host com as opções usadas com frequência, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="0dde3-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="0dde3-148">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="0dde3-149">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="0dde3-150">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="0dde3-151">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="0dde3-152">Cenários não Web</span><span class="sxs-lookup"><span data-stu-id="0dde3-152">Non-web scenarios</span></span>

<span data-ttu-id="0dde3-153">O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0dde3-153">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="0dde3-154">Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-154">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="0dde3-155">Servidores</span><span class="sxs-lookup"><span data-stu-id="0dde3-155">Servers</span></span>

<span data-ttu-id="0dde3-156">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0dde3-156">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="0dde3-157">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-157">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="0dde3-158">Windows</span><span class="sxs-lookup"><span data-stu-id="0dde3-158">Windows</span></span>](#tab/windows)

<span data-ttu-id="0dde3-159">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="0dde3-159">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="0dde3-160">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="0dde3-160">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="0dde3-161">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-161">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="0dde3-162">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-162">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="0dde3-163">O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-163">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="0dde3-164">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-164">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="0dde3-165">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-165">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="0dde3-166">macOS</span><span class="sxs-lookup"><span data-stu-id="0dde3-166">macOS</span></span>](#tab/macos)

<span data-ttu-id="0dde3-167">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-167">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0dde3-168">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-168">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0dde3-169">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-169">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="0dde3-170">Linux</span><span class="sxs-lookup"><span data-stu-id="0dde3-170">Linux</span></span>](#tab/linux)

<span data-ttu-id="0dde3-171">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-171">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0dde3-172">No ASP.NET Core 2.0 ou posterior, o Kestrel também pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-172">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0dde3-173">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-173">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="0dde3-174">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-174">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="0dde3-175">Configuração</span><span class="sxs-lookup"><span data-stu-id="0dde3-175">Configuration</span></span>

<span data-ttu-id="0dde3-176">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-176">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="0dde3-177">Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0dde3-177">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="0dde3-178">Você também pode escrever seus próprios provedores de configuração personalizados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-178">You can also write custom configuration providers.</span></span>

<span data-ttu-id="0dde3-179">Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0dde3-179">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="0dde3-180">Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-180">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="0dde3-181">Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0dde3-181">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="0dde3-182">Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="0dde3-182">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0dde3-183">Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="0dde3-183">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="0dde3-184">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="0dde3-185">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="0dde3-185">Options</span></span>

<span data-ttu-id="0dde3-186">Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-186">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="0dde3-187">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="0dde3-187">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="0dde3-188">Por exemplo, o código a seguir define as opções de WebSockets:</span><span class="sxs-lookup"><span data-stu-id="0dde3-188">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="0dde3-189">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-189">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="0dde3-190">Ambientes</span><span class="sxs-lookup"><span data-stu-id="0dde3-190">Environments</span></span>

<span data-ttu-id="0dde3-191">Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-191">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="0dde3-192">Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-192">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="0dde3-193">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-193">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="0dde3-194">O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="0dde3-194">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="0dde3-195">O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="0dde3-195">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="0dde3-196">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-196">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="0dde3-197">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="0dde3-197">Logging</span></span>

<span data-ttu-id="0dde3-198">O ASP.NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-198">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="0dde3-199">Provedores disponíveis incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="0dde3-199">Available providers include the following:</span></span>

* <span data-ttu-id="0dde3-200">Console</span><span class="sxs-lookup"><span data-stu-id="0dde3-200">Console</span></span>
* <span data-ttu-id="0dde3-201">Depuração</span><span class="sxs-lookup"><span data-stu-id="0dde3-201">Debug</span></span>
* <span data-ttu-id="0dde3-202">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="0dde3-202">Event Tracing on Windows</span></span>
* <span data-ttu-id="0dde3-203">Log de eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="0dde3-203">Windows Event Log</span></span>
* <span data-ttu-id="0dde3-204">TraceSource</span><span class="sxs-lookup"><span data-stu-id="0dde3-204">TraceSource</span></span>
* <span data-ttu-id="0dde3-205">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="0dde3-205">Azure App Service</span></span>
* <span data-ttu-id="0dde3-206">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="0dde3-206">Azure Application Insights</span></span>

<span data-ttu-id="0dde3-207">Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.</span><span class="sxs-lookup"><span data-stu-id="0dde3-207">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="0dde3-208">Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.</span><span class="sxs-lookup"><span data-stu-id="0dde3-208">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="0dde3-209">A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log.</span><span class="sxs-lookup"><span data-stu-id="0dde3-209">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="0dde3-210">Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-210">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="0dde3-211">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="0dde3-211">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="0dde3-212">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="0dde3-213">Roteamento</span><span class="sxs-lookup"><span data-stu-id="0dde3-213">Routing</span></span>

<span data-ttu-id="0dde3-214">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="0dde3-214">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="0dde3-215">O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="0dde3-215">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="0dde3-216">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-216">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="0dde3-217">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-217">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="0dde3-218">Manipulação de erros</span><span class="sxs-lookup"><span data-stu-id="0dde3-218">Error handling</span></span>

<span data-ttu-id="0dde3-219">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-219">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="0dde3-220">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="0dde3-220">A developer exception page</span></span>
* <span data-ttu-id="0dde3-221">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="0dde3-221">Custom error pages</span></span>
* <span data-ttu-id="0dde3-222">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="0dde3-222">Static status code pages</span></span>
* <span data-ttu-id="0dde3-223">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="0dde3-223">Startup exception handling</span></span>

<span data-ttu-id="0dde3-224">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-224">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="0dde3-225">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="0dde3-225">Make HTTP requests</span></span>

<span data-ttu-id="0dde3-226">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-226">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="0dde3-227">O alocador:</span><span class="sxs-lookup"><span data-stu-id="0dde3-227">The factory:</span></span>

* <span data-ttu-id="0dde3-228">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-228">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="0dde3-229">Por exemplo, um cliente *github* pode ser registrado e configurado para acessar o GitHub.</span><span class="sxs-lookup"><span data-stu-id="0dde3-229">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="0dde3-230">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="0dde3-230">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="0dde3-231">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="0dde3-231">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="0dde3-232">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-232">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="0dde3-233">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="0dde3-233">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="0dde3-234">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="0dde3-234">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="0dde3-235">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-235">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="0dde3-236">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="0dde3-236">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="0dde3-237">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-237">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="0dde3-238">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="0dde3-238">Content root</span></span>

<span data-ttu-id="0dde3-239">A raiz do conteúdo é o caminho base para:</span><span class="sxs-lookup"><span data-stu-id="0dde3-239">The content root is the base path to the:</span></span>

* <span data-ttu-id="0dde3-240">Executável que hospeda o aplicativo ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="0dde3-240">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="0dde3-241">Assemblies compilados que compõem o aplicativo ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="0dde3-241">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="0dde3-242">Arquivos de conteúdo que não são de código usados pelo aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-242">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="0dde3-243">Arquivos Razor ( *. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-243">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="0dde3-244">Arquivos de configuração ( *. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-244">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="0dde3-245">Arquivos de dados ( *. db*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-245">Data files (*.db*)</span></span>
* <span data-ttu-id="0dde3-246">[Raiz da Web](#web-root), normalmente a pasta *wwwroot* publicada.</span><span class="sxs-lookup"><span data-stu-id="0dde3-246">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="0dde3-247">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="0dde3-247">During development:</span></span>

* <span data-ttu-id="0dde3-248">A raiz do conteúdo assume como padrão o diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-248">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="0dde3-249">O diretório raiz do projeto é usado para criar:</span><span class="sxs-lookup"><span data-stu-id="0dde3-249">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="0dde3-250">Caminho para os arquivos de conteúdo sem código do aplicativo no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-250">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="0dde3-251">[Raiz da Web](#web-root), normalmente a pasta *wwwroot* no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-251">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="0dde3-252">Um caminho de raiz de conteúdo alternativo pode ser especificado ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="0dde3-252">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="0dde3-253">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#contentrootpath>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-253">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

## <a name="web-root"></a><span data-ttu-id="0dde3-254">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="0dde3-254">Web root</span></span>

<span data-ttu-id="0dde3-255">A raiz da Web é o caminho base para arquivos de recurso estáticos, não de código e públicos, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-255">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="0dde3-256">Folhas de estilo ( *. css*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-256">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="0dde3-257">JavaScript ( *. js*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-257">JavaScript (*.js*)</span></span>
* <span data-ttu-id="0dde3-258">Imagens ( *. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-258">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="0dde3-259">Os arquivos estáticos são servidos apenas por padrão no diretório raiz da Web (e subdiretórios).</span><span class="sxs-lookup"><span data-stu-id="0dde3-259">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="0dde3-260">O caminho raiz da Web tem como padrão *{Content root}/wwwroot*, mas uma raiz da Web diferente pode ser especificada ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="0dde3-260">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="0dde3-261">Para obter mais informações, consulte <xref:fundamentals/host/generic-host#webroot>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-261">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

<span data-ttu-id="0dde3-262">Impedir a publicação de arquivos em *wwwroot* com o [\<conteúdo > item de projeto](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-262">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="0dde3-263">O exemplo a seguir impede a publicação de conteúdo no diretório *wwwroot/local* e nos subpastas:</span><span class="sxs-lookup"><span data-stu-id="0dde3-263">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="0dde3-264">Para evitar a publicação de ativos de identidade estática na raiz da Web, consulte <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-264">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

<span data-ttu-id="0dde3-265">Em arquivos Razor ( *. cshtml*), a barra de tils (`~/`) aponta para a raiz da Web.</span><span class="sxs-lookup"><span data-stu-id="0dde3-265">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="0dde3-266">Um caminho que começa com `~/` é conhecido como um *caminho virtual*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-266">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="0dde3-267">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-267">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0dde3-268">Este artigo é uma visão geral dos principais tópicos para entender como desenvolver aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-268">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="0dde3-269">A classe Startup</span><span class="sxs-lookup"><span data-stu-id="0dde3-269">The Startup class</span></span>

<span data-ttu-id="0dde3-270">A classe `Startup` é o local em que:</span><span class="sxs-lookup"><span data-stu-id="0dde3-270">The `Startup` class is where:</span></span>

* <span data-ttu-id="0dde3-271">Os serviços exigidos pelo aplicativo são configurados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-271">Services required by the app are configured.</span></span>
* <span data-ttu-id="0dde3-272">O pipeline de tratamento de solicitação é definido.</span><span class="sxs-lookup"><span data-stu-id="0dde3-272">The request handling pipeline is defined.</span></span>

<span data-ttu-id="0dde3-273">*Serviços* são componentes usados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-273">*Services* are components that are used by the app.</span></span> <span data-ttu-id="0dde3-274">Por exemplo, um componente de registro em log é um serviço.</span><span class="sxs-lookup"><span data-stu-id="0dde3-274">For example, a logging component is a service.</span></span> <span data-ttu-id="0dde3-275">Código para configurar (ou *registrar*) serviços é adicionado ao método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-275">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="0dde3-276">O pipeline de tratamento de solicitações é composto como uma série de componentes de *middleware*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-276">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="0dde3-277">Por exemplo, um middleware pode manipular as solicitações para arquivos estáticos ou redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-277">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="0dde3-278">Cada middleware executa operações assíncronas em um `HttpContext` e invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0dde3-278">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="0dde3-279">O código para configurar a pipeline de tratamento de solicitação é adicionado ao método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-279">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="0dde3-280">Aqui está um exemplo de classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="0dde3-280">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="0dde3-281">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="0dde3-282">Injeção de dependência (serviços)</span><span class="sxs-lookup"><span data-stu-id="0dde3-282">Dependency injection (services)</span></span>

<span data-ttu-id="0dde3-283">O ASP.NET Core tem uma estrutura de DI (injeção de dependência) interna que torna serviços configurados disponíveis para classes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-283">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="0dde3-284">Uma maneira de obter uma instância de um serviço em uma classe criar um construtor com um parâmetro do tipo necessário.</span><span class="sxs-lookup"><span data-stu-id="0dde3-284">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="0dde3-285">O parâmetro pode ser o tipo de serviço ou uma interface.</span><span class="sxs-lookup"><span data-stu-id="0dde3-285">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="0dde3-286">O sistema de DI fornece o serviço em runtime.</span><span class="sxs-lookup"><span data-stu-id="0dde3-286">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="0dde3-287">Aqui está uma classe que usa DI para obter um objeto de contexto do Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-287">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="0dde3-288">A linha realçada é um exemplo de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="0dde3-288">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="0dde3-289">Embora a DI seja interna, ela foi projetada para permitir que você conecte um contêiner de IoC (inversão de controle) de terceiros, se preferir.</span><span class="sxs-lookup"><span data-stu-id="0dde3-289">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="0dde3-290">Para obter mais informações, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-290">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="0dde3-291">Middleware</span><span class="sxs-lookup"><span data-stu-id="0dde3-291">Middleware</span></span>

<span data-ttu-id="0dde3-292">O pipeline de tratamento de solicitação é composto como uma série de componentes de middleware.</span><span class="sxs-lookup"><span data-stu-id="0dde3-292">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="0dde3-293">Cada componente executa operações assíncronas em um `HttpContext` e então invoca o próximo middleware no pipeline ou encerra a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0dde3-293">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="0dde3-294">Por convenção, um componente de middleware é adicionado ao pipeline invocando seu método de extensão `Use...` no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-294">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="0dde3-295">Por exemplo, para habilitar o processamento de arquivos estáticos, chame `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-295">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="0dde3-296">O código realçado no exemplo a seguir configura o pipeline de tratamento de solicitação:</span><span class="sxs-lookup"><span data-stu-id="0dde3-296">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="0dde3-297">O ASP.NET Core inclui um conjunto de middleware interno, e você pode escrever um middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="0dde3-297">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="0dde3-298">Para obter mais informações, consulte <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-298">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="0dde3-299">Host</span><span class="sxs-lookup"><span data-stu-id="0dde3-299">Host</span></span>

<span data-ttu-id="0dde3-300">Um aplicativo ASP.NET Core cria um *host* na inicialização.</span><span class="sxs-lookup"><span data-stu-id="0dde3-300">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="0dde3-301">O host é um objeto que encapsula todos os recursos do aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-301">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="0dde3-302">Uma implementação do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="0dde3-302">An HTTP server implementation</span></span>
* <span data-ttu-id="0dde3-303">Componentes de middleware</span><span class="sxs-lookup"><span data-stu-id="0dde3-303">Middleware components</span></span>
* <span data-ttu-id="0dde3-304">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="0dde3-304">Logging</span></span>
* <span data-ttu-id="0dde3-305">DI</span><span class="sxs-lookup"><span data-stu-id="0dde3-305">DI</span></span>
* <span data-ttu-id="0dde3-306">Configuração</span><span class="sxs-lookup"><span data-stu-id="0dde3-306">Configuration</span></span>

<span data-ttu-id="0dde3-307">O principal motivo para incluir todos os recursos interdependentes do aplicativo em um objeto é o gerenciamento de tempo de vida: controle sobre a inicialização do aplicativo e desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="0dde3-307">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="0dde3-308">Dois hosts estão disponíveis: o Host Web e o Host Genérico.</span><span class="sxs-lookup"><span data-stu-id="0dde3-308">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="0dde3-309">No ASP.NET Core 2.x, o Host Genérico é somente para cenários não Web.</span><span class="sxs-lookup"><span data-stu-id="0dde3-309">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="0dde3-310">O código para criar um host está em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0dde3-310">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="0dde3-311">O método `CreateDefaultBuilder` configura um host com as opções usadas com frequência, como as seguintes:</span><span class="sxs-lookup"><span data-stu-id="0dde3-311">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="0dde3-312">Uso do [Kestrel](#servers) como o servidor Web e habilitação da integração do IIS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-312">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="0dde3-313">Configuração de carregamento de *appsettings.json*, *appsettings.{EnvironmentName}.json*, de variáveis de ambiente, de argumentos de linha de comando e outras fontes de configuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-313">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="0dde3-314">Envio da saída de log para os provedores de console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-314">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="0dde3-315">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-315">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="0dde3-316">Cenários não Web</span><span class="sxs-lookup"><span data-stu-id="0dde3-316">Non-web scenarios</span></span>

<span data-ttu-id="0dde3-317">O Host Genérico permite que outros tipos de aplicativos usem extensões de estruturas abrangentes como registro em log, DI (Injeção de Dependência), configuração e gerenciamento do tempo de vida dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0dde3-317">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="0dde3-318">Para obter mais informações, consulte <xref:fundamentals/host/generic-host> e <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-318">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="0dde3-319">Servidores</span><span class="sxs-lookup"><span data-stu-id="0dde3-319">Servers</span></span>

<span data-ttu-id="0dde3-320">Um aplicativo ASP.NET Core usa uma implementação do servidor HTTP para ouvir solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0dde3-320">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="0dde3-321">O servidor descobre solicitações ao aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-321">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="0dde3-322">Windows</span><span class="sxs-lookup"><span data-stu-id="0dde3-322">Windows</span></span>](#tab/windows)

<span data-ttu-id="0dde3-323">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="0dde3-323">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="0dde3-324">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="0dde3-324">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="0dde3-325">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-325">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="0dde3-326">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-326">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="0dde3-327">O *Servidor HTTP de IIS* é um servidor do Windows que usa o IIS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-327">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="0dde3-328">Com esse servidor, o aplicativo ASP.NET Core e o IIS são executados no mesmo processo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-328">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="0dde3-329">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-329">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="0dde3-330">macOS</span><span class="sxs-lookup"><span data-stu-id="0dde3-330">macOS</span></span>](#tab/macos)

<span data-ttu-id="0dde3-331">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-331">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0dde3-332">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-332">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0dde3-333">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-333">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="0dde3-334">Linux</span><span class="sxs-lookup"><span data-stu-id="0dde3-334">Linux</span></span>](#tab/linux)

<span data-ttu-id="0dde3-335">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-335">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0dde3-336">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-336">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0dde3-337">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-337">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="0dde3-338">Windows</span><span class="sxs-lookup"><span data-stu-id="0dde3-338">Windows</span></span>](#tab/windows)

<span data-ttu-id="0dde3-339">O ASP.NET Core vem com as seguintes implementações de servidor:</span><span class="sxs-lookup"><span data-stu-id="0dde3-339">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="0dde3-340">*Kestrel* é um servidor Web multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="0dde3-340">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="0dde3-341">O Kestrel normalmente é executado em uma configuração de proxy reverso que usa o [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-341">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="0dde3-342">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-342">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="0dde3-343">*HTTP.sys* é um servidor para Windows que não é usado com IIS.</span><span class="sxs-lookup"><span data-stu-id="0dde3-343">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="0dde3-344">macOS</span><span class="sxs-lookup"><span data-stu-id="0dde3-344">macOS</span></span>](#tab/macos)

<span data-ttu-id="0dde3-345">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-345">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0dde3-346">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-346">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0dde3-347">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-347">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="0dde3-348">Linux</span><span class="sxs-lookup"><span data-stu-id="0dde3-348">Linux</span></span>](#tab/linux)

<span data-ttu-id="0dde3-349">O ASP.NET Core fornece a implementação de servidor multiplataforma do *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-349">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0dde3-350">Kestrel pode ser executado como um servidor de borda voltado para o público exposto diretamente à Internet.</span><span class="sxs-lookup"><span data-stu-id="0dde3-350">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0dde3-351">O Kestrel normalmente é executado em uma configuração de proxy reverso com [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0dde3-351">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0dde3-352">Para obter mais informações, consulte <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-352">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="0dde3-353">Configuração</span><span class="sxs-lookup"><span data-stu-id="0dde3-353">Configuration</span></span>

<span data-ttu-id="0dde3-354">O ASP.NET Core fornece uma estrutura de configuração que obtém as configurações como pares nome-valor de um conjunto ordenado de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-354">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="0dde3-355">Há provedores de configuração internos para uma variedade de fontes, como arquivos *.json*, arquivos *.xml*, variáveis de ambiente e argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0dde3-355">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="0dde3-356">Você também pode escrever seus próprios provedores de configuração personalizados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-356">You can also write custom configuration providers.</span></span>

<span data-ttu-id="0dde3-357">Por exemplo, você poderia especificar que a configuração é proveniente de *appsettings.json* e variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0dde3-357">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="0dde3-358">Então, quando o valor de *ConnectionString* for solicitado, a estrutura procura primeiro no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-358">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="0dde3-359">Se o valor for encontrado lá, mas também em uma variável de ambiente, o valor da variável de ambiente terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0dde3-359">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="0dde3-360">Para gerenciar dados de configuração confidenciais como senhas, o ASP.NET Core fornece uma [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="0dde3-360">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0dde3-361">Para segredos de produção, recomendamos o [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="0dde3-361">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="0dde3-362">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-362">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="0dde3-363">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="0dde3-363">Options</span></span>

<span data-ttu-id="0dde3-364">Sempre que possível, o ASP.NET Core segue o *padrão de opções* para armazenar e recuperar valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="0dde3-364">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="0dde3-365">O padrão de opções usa classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="0dde3-365">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="0dde3-366">Por exemplo, o código a seguir define as opções de WebSockets:</span><span class="sxs-lookup"><span data-stu-id="0dde3-366">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="0dde3-367">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-367">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="0dde3-368">Ambientes</span><span class="sxs-lookup"><span data-stu-id="0dde3-368">Environments</span></span>

<span data-ttu-id="0dde3-369">Ambientes de execução, como *Desenvolvimento*, *Preparo* e *Produção*, são uma noção de primeira classe no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-369">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="0dde3-370">Você pode especificar o ambiente em que um aplicativo está em execução definindo a variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-370">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="0dde3-371">O ASP.NET Core lê a variável de ambiente na inicialização do aplicativo e armazena o valor em uma implementação `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-371">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="0dde3-372">O objeto de ambiente está disponível em qualquer lugar no aplicativo por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="0dde3-372">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="0dde3-373">O seguinte código de exemplo da classe `Startup` configura o aplicativo para fornecer informações de erro detalhadas somente quando ele é executado no desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="0dde3-373">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="0dde3-374">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-374">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="0dde3-375">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="0dde3-375">Logging</span></span>

<span data-ttu-id="0dde3-376">O ASP.NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-376">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="0dde3-377">Provedores disponíveis incluem os seguintes:</span><span class="sxs-lookup"><span data-stu-id="0dde3-377">Available providers include the following:</span></span>

* <span data-ttu-id="0dde3-378">Console</span><span class="sxs-lookup"><span data-stu-id="0dde3-378">Console</span></span>
* <span data-ttu-id="0dde3-379">Depuração</span><span class="sxs-lookup"><span data-stu-id="0dde3-379">Debug</span></span>
* <span data-ttu-id="0dde3-380">Rastreamento de Eventos no Windows</span><span class="sxs-lookup"><span data-stu-id="0dde3-380">Event Tracing on Windows</span></span>
* <span data-ttu-id="0dde3-381">Log de eventos do Windows</span><span class="sxs-lookup"><span data-stu-id="0dde3-381">Windows Event Log</span></span>
* <span data-ttu-id="0dde3-382">TraceSource</span><span class="sxs-lookup"><span data-stu-id="0dde3-382">TraceSource</span></span>
* <span data-ttu-id="0dde3-383">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="0dde3-383">Azure App Service</span></span>
* <span data-ttu-id="0dde3-384">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="0dde3-384">Azure Application Insights</span></span>

<span data-ttu-id="0dde3-385">Escreva logs de qualquer lugar no código do aplicativo obtendo um objeto `ILogger` da DI e chamando os métodos de log.</span><span class="sxs-lookup"><span data-stu-id="0dde3-385">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="0dde3-386">Aqui está o código de exemplo que usa um objeto `ILogger`, com injeção de construtor e chamadas de método de registro em log realçadas.</span><span class="sxs-lookup"><span data-stu-id="0dde3-386">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="0dde3-387">A interface `ILogger` permite que você passe qualquer número de campos para o provedor de registro em log.</span><span class="sxs-lookup"><span data-stu-id="0dde3-387">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="0dde3-388">Os campos são comumente usados para construir uma cadeia de caracteres de mensagem, mas o provedor também pode enviá-los como campos separados para um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="0dde3-388">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="0dde3-389">Esse recurso torna possível para provedores de log implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="0dde3-389">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="0dde3-390">Para obter mais informações, consulte <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-390">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="0dde3-391">Roteamento</span><span class="sxs-lookup"><span data-stu-id="0dde3-391">Routing</span></span>

<span data-ttu-id="0dde3-392">Um *rota* é um padrão de URL mapeado para um manipulador.</span><span class="sxs-lookup"><span data-stu-id="0dde3-392">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="0dde3-393">O manipulador normalmente é um Razor Page, um método de ação em um controlador MVC ou um middleware.</span><span class="sxs-lookup"><span data-stu-id="0dde3-393">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="0dde3-394">O roteamento do ASP.NET Core lhe dá controle sobre as URLs usadas pelo seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dde3-394">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="0dde3-395">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-395">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="0dde3-396">Manipulação de erros</span><span class="sxs-lookup"><span data-stu-id="0dde3-396">Error handling</span></span>

<span data-ttu-id="0dde3-397">O ASP.NET Core tem recursos internos para tratamento de erros, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-397">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="0dde3-398">Uma página de exceção do desenvolvedor</span><span class="sxs-lookup"><span data-stu-id="0dde3-398">A developer exception page</span></span>
* <span data-ttu-id="0dde3-399">Páginas de erro personalizadas</span><span class="sxs-lookup"><span data-stu-id="0dde3-399">Custom error pages</span></span>
* <span data-ttu-id="0dde3-400">Páginas de código de status estático</span><span class="sxs-lookup"><span data-stu-id="0dde3-400">Static status code pages</span></span>
* <span data-ttu-id="0dde3-401">Tratamento de exceção na inicialização</span><span class="sxs-lookup"><span data-stu-id="0dde3-401">Startup exception handling</span></span>

<span data-ttu-id="0dde3-402">Para obter mais informações, consulte <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-402">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="0dde3-403">Fazer solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="0dde3-403">Make HTTP requests</span></span>

<span data-ttu-id="0dde3-404">Uma implementação de `IHttpClientFactory` está disponível para a criação de instâncias do `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-404">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="0dde3-405">O alocador:</span><span class="sxs-lookup"><span data-stu-id="0dde3-405">The factory:</span></span>

* <span data-ttu-id="0dde3-406">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-406">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="0dde3-407">Por exemplo, um cliente *github* pode ser registrado e configurado para acessar o GitHub.</span><span class="sxs-lookup"><span data-stu-id="0dde3-407">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="0dde3-408">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="0dde3-408">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="0dde3-409">Dá suporte ao registro e ao encadeamento de vários manipuladores de delegação para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="0dde3-409">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="0dde3-410">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0dde3-410">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="0dde3-411">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="0dde3-411">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="0dde3-412">Integra-se com a *Polly*, uma biblioteca de terceiros popular para tratamento de falhas transitórias.</span><span class="sxs-lookup"><span data-stu-id="0dde3-412">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="0dde3-413">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="0dde3-413">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="0dde3-414">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="0dde3-414">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="0dde3-415">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-415">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="0dde3-416">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="0dde3-416">Content root</span></span>

<span data-ttu-id="0dde3-417">A raiz do conteúdo é o caminho base para:</span><span class="sxs-lookup"><span data-stu-id="0dde3-417">The content root is the base path to the:</span></span>

* <span data-ttu-id="0dde3-418">Executável que hospeda o aplicativo ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="0dde3-418">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="0dde3-419">Assemblies compilados que compõem o aplicativo ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="0dde3-419">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="0dde3-420">Arquivos de conteúdo que não são de código usados pelo aplicativo, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-420">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="0dde3-421">Arquivos Razor ( *. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-421">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="0dde3-422">Arquivos de configuração ( *. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-422">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="0dde3-423">Arquivos de dados ( *. db*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-423">Data files (*.db*)</span></span>
* <span data-ttu-id="0dde3-424">[Raiz da Web](#web-root), normalmente a pasta *wwwroot* publicada.</span><span class="sxs-lookup"><span data-stu-id="0dde3-424">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="0dde3-425">Durante o desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="0dde3-425">During development:</span></span>

* <span data-ttu-id="0dde3-426">A raiz do conteúdo assume como padrão o diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-426">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="0dde3-427">O diretório raiz do projeto é usado para criar:</span><span class="sxs-lookup"><span data-stu-id="0dde3-427">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="0dde3-428">Caminho para os arquivos de conteúdo sem código do aplicativo no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-428">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="0dde3-429">[Raiz da Web](#web-root), normalmente a pasta *wwwroot* no diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-429">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="0dde3-430">Um caminho de raiz de conteúdo alternativo pode ser especificado ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="0dde3-430">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="0dde3-431">Para obter mais informações, consulte <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-431">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="0dde3-432">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="0dde3-432">Web root</span></span>

<span data-ttu-id="0dde3-433">A raiz da Web é o caminho base para arquivos de recurso estáticos, não de código e públicos, como:</span><span class="sxs-lookup"><span data-stu-id="0dde3-433">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="0dde3-434">Folhas de estilo ( *. css*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-434">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="0dde3-435">JavaScript ( *. js*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-435">JavaScript (*.js*)</span></span>
* <span data-ttu-id="0dde3-436">Imagens ( *. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="0dde3-436">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="0dde3-437">Os arquivos estáticos são servidos apenas por padrão no diretório raiz da Web (e subdiretórios).</span><span class="sxs-lookup"><span data-stu-id="0dde3-437">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="0dde3-438">O caminho raiz da Web tem como padrão *{Content root}/wwwroot*, mas uma raiz da Web diferente pode ser especificada ao [criar o host](#host).</span><span class="sxs-lookup"><span data-stu-id="0dde3-438">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="0dde3-439">Para obter mais informações, confira [Diretório base](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="0dde3-439">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="0dde3-440">Impedir a publicação de arquivos em *wwwroot* com o [\<conteúdo > item de projeto](/visualstudio/msbuild/common-msbuild-project-items#content) no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="0dde3-440">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="0dde3-441">O exemplo a seguir impede a publicação de conteúdo no diretório *wwwroot/local* e nos subpastas:</span><span class="sxs-lookup"><span data-stu-id="0dde3-441">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="0dde3-442">Em arquivos Razor ( *. cshtml*), a barra de tils (`~/`) aponta para a raiz da Web.</span><span class="sxs-lookup"><span data-stu-id="0dde3-442">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="0dde3-443">Um caminho que começa com `~/` é conhecido como um *caminho virtual*.</span><span class="sxs-lookup"><span data-stu-id="0dde3-443">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="0dde3-444">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="0dde3-444">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end