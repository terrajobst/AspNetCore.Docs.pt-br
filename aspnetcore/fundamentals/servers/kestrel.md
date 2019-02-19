---
title: Implementação do servidor Web Kestrel no ASP.NET Core
author: guardrex
description: Saiba mais sobre o Kestrel, o servidor Web multiplataforma do ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/13/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: dcf027c2c495cbecd8464e43749b9154a4360e36
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248401"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="327ae-103">Implementação do servidor Web Kestrel no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="327ae-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="327ae-104">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="327ae-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="327ae-105">Para obter a versão 1.1 deste tópico, baixe [Implementação do servidor Web Kestrel no ASP.NET Core (versão 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Kestrel_1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="327ae-105">For the 1.1 version of this topic, download [Kestrel web server implementation in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Kestrel_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="327ae-106">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="327ae-106">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="327ae-107">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="327ae-107">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="327ae-108">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="327ae-108">Kestrel supports the following scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="327ae-109">HTTPS</span><span class="sxs-lookup"><span data-stu-id="327ae-109">HTTPS</span></span>
* <span data-ttu-id="327ae-110">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="327ae-110">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="327ae-111">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="327ae-111">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="327ae-112">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="327ae-112">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="327ae-113">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="327ae-113">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="327ae-114">HTTPS</span><span class="sxs-lookup"><span data-stu-id="327ae-114">HTTPS</span></span>
* <span data-ttu-id="327ae-115">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="327ae-115">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="327ae-116">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="327ae-116">Unix sockets for high performance behind Nginx</span></span>

::: moniker-end

<span data-ttu-id="327ae-117">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="327ae-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="327ae-118">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="327ae-118">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="http2-support"></a><span data-ttu-id="327ae-119">Compatibilidade com HTTP/2</span><span class="sxs-lookup"><span data-stu-id="327ae-119">HTTP/2 support</span></span>

<span data-ttu-id="327ae-120">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="327ae-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="327ae-121">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="327ae-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="327ae-122">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="327ae-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="327ae-123">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="327ae-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="327ae-124">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="327ae-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="327ae-125">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="327ae-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="327ae-126">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="327ae-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="327ae-127">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="327ae-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="327ae-128">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="327ae-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="327ae-129">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="327ae-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="327ae-130">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="327ae-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="327ae-131">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="327ae-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="327ae-132">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="327ae-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="327ae-133">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="327ae-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

::: moniker-end

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="327ae-134">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="327ae-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="327ae-135">Você pode usar o Kestrel sozinho ou com um *servidor proxy reverso* como [IIS (Serviços de Informações da Internet)](https://www.iis.net/), [Nginx](http://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="327ae-135">You can use Kestrel by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="327ae-136">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-136">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="327ae-137">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="327ae-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="327ae-139">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="327ae-139">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="327ae-141">Qualquer configuração – com ou sem um servidor proxy reverso – é uma configuração de hospedagem compatível com o ASP.NET Core 2.1 ou aplicativos posteriores que recebem solicitações da Internet.</span><span class="sxs-lookup"><span data-stu-id="327ae-141">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration for ASP.NET Core 2.1 or later apps that receive requests from the Internet.</span></span>

<span data-ttu-id="327ae-142">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="327ae-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="327ae-143">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="327ae-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="327ae-144">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="327ae-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="327ae-145">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="327ae-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="327ae-146">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="327ae-146">A reverse proxy:</span></span>

* <span data-ttu-id="327ae-147">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="327ae-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="327ae-148">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="327ae-148">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="327ae-149">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="327ae-149">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="327ae-150">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="327ae-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="327ae-151">Somente o servidor proxy reverso exige um certificado X.509 e esse servidor pode se comunicar com os servidores de aplicativos na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="327ae-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with your app servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="327ae-152">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="327ae-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="327ae-153">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="327ae-153">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="327ae-154">O pacote [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="327ae-154">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="327ae-155">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="327ae-155">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="327ae-156">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="327ae-156">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="327ae-157">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="327ae-157">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        });
```

<span data-ttu-id="327ae-158">Se o aplicativo não chamar `CreateDefaultBuilder` para configurar o host, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **antes** de chamar `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="327ae-158">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        })
        .Build();

    host.Run();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="327ae-159">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="327ae-159">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

## <a name="kestrel-options"></a><span data-ttu-id="327ae-160">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="327ae-160">Kestrel options</span></span>

<span data-ttu-id="327ae-161">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="327ae-161">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="327ae-162">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="327ae-162">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="327ae-163">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="327ae-163">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

### <a name="maximum-client-connections"></a><span data-ttu-id="327ae-164">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="327ae-164">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>  
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="327ae-165">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="327ae-165">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="327ae-166">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="327ae-166">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="327ae-167">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="327ae-167">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="327ae-168">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="327ae-168">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="327ae-169">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="327ae-169">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="327ae-170">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="327ae-170">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="327ae-171">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="327ae-171">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="327ae-172">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="327ae-172">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="327ae-173">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="327ae-173">You can override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

::: moniker-end

<span data-ttu-id="327ae-174">Uma exceção será gerada se você tentar configurar o limite em uma solicitação depois que o aplicativo começar a ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="327ae-174">An exception is thrown if you attempt to configure the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="327ae-175">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="327ae-175">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="327ae-176">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="327ae-176">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>  
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="327ae-177">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="327ae-177">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="327ae-178">Se a taxa cair abaixo do mínimo, a conexão atingirá o tempo limite. O período de cortesia é o tempo que o Kestrel fornece ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="327ae-178">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="327ae-179">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="327ae-179">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="327ae-180">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="327ae-180">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="327ae-181">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="327ae-181">A minimum rate also applies to the response.</span></span> <span data-ttu-id="327ae-182">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="327ae-182">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="327ae-183">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="327ae-183">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            options.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

::: moniker-end

<span data-ttu-id="327ae-184">Você pode substituir os limites de taxa mínima por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="327ae-184">You can override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="327ae-185">Nenhum desses recursos de taxa referenciados no exemplo anterior está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="327ae-185">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="327ae-186">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="327ae-186">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="327ae-187">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="327ae-187">Maximum streams per connection</span></span>

<span data-ttu-id="327ae-188">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="327ae-188">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="327ae-189">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="327ae-189">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="327ae-190">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="327ae-190">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="327ae-191">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="327ae-191">Header table size</span></span>

<span data-ttu-id="327ae-192">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="327ae-192">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="327ae-193">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="327ae-193">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="327ae-194">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="327ae-194">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="327ae-195">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="327ae-195">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="327ae-196">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="327ae-196">Maximum frame size</span></span>

<span data-ttu-id="327ae-197">O `Http2.MaxFrameSize` indica o tamanho máximo do payload do quadro da conexão HTTP/2 a ser recebido.</span><span class="sxs-lookup"><span data-stu-id="327ae-197">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="327ae-198">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="327ae-198">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="327ae-199">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="327ae-199">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="327ae-200">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="327ae-200">Maximum request header size</span></span>

<span data-ttu-id="327ae-201">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="327ae-201">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="327ae-202">Esse limite se aplica ao nome e ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="327ae-202">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="327ae-203">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="327ae-203">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="327ae-204">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="327ae-204">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="327ae-205">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="327ae-205">Initial connection window size</span></span>

<span data-ttu-id="327ae-206">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="327ae-206">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="327ae-207">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="327ae-207">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="327ae-208">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="327ae-208">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="327ae-209">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="327ae-209">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="327ae-210">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="327ae-210">Initial stream window size</span></span>

<span data-ttu-id="327ae-211">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="327ae-211">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="327ae-212">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="327ae-212">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="327ae-213">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="327ae-213">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="327ae-214">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="327ae-214">The default value is 96 KB (98,304).</span></span>

::: moniker-end

<span data-ttu-id="327ae-215">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="327ae-215">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="327ae-216">Configuração do ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="327ae-216">Endpoint configuration</span></span>

<span data-ttu-id="327ae-217">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="327ae-217">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="327ae-218">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="327ae-218">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="327ae-219">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="327ae-219">A development certificate is created:</span></span>

* <span data-ttu-id="327ae-220">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="327ae-220">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="327ae-221">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="327ae-221">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="327ae-222">Alguns navegadores exigem que você conceda permissão explícita para o navegador para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="327ae-222">Some browsers require that you grant explicit permission to the browser to trust the local development certificate.</span></span>

<span data-ttu-id="327ae-223">Os modelos de projeto do ASP.NET Core 2.1 e posteriores configuram os aplicativos para serem executados em HTTPS, por padrão, e incluem o [Redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="327ae-223">ASP.NET Core 2.1 and later project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="327ae-224">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-224">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="327ae-225">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="327ae-225">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="327ae-226">Configuração do ASP.NET Core 2.1 `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="327ae-226">ASP.NET Core 2.1 `KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionltlistenoptionsgt"></a><span data-ttu-id="327ae-227">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="327ae-227">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span></span>

<span data-ttu-id="327ae-228">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="327ae-228">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="327ae-229">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="327ae-229">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

### <a name="configurehttpsdefaultsactionlthttpsconnectionadapteroptionsgt"></a><span data-ttu-id="327ae-230">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="327ae-230">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span></span>

<span data-ttu-id="327ae-231">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="327ae-231">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="327ae-232">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="327ae-232">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="327ae-233">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="327ae-233">Configure(IConfiguration)</span></span>

<span data-ttu-id="327ae-234">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="327ae-234">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="327ae-235">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-235">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="327ae-236">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="327ae-236">ListenOptions.UseHttps</span></span>

<span data-ttu-id="327ae-237">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="327ae-237">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="327ae-238">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="327ae-238">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="327ae-239">`UseHttps` &ndash; Configure o Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="327ae-239">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="327ae-240">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="327ae-240">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="327ae-241">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="327ae-241">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="327ae-242">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="327ae-242">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="327ae-243">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="327ae-243">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="327ae-244">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="327ae-244">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="327ae-245">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="327ae-245">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="327ae-246">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="327ae-246">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="327ae-247">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="327ae-247">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="327ae-248">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="327ae-248">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="327ae-249">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="327ae-249">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="327ae-250">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="327ae-250">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="327ae-251">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="327ae-251">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="327ae-252">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="327ae-252">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="327ae-253">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="327ae-253">Supported configurations described next:</span></span>

* <span data-ttu-id="327ae-254">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="327ae-254">No configuration</span></span>
* <span data-ttu-id="327ae-255">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="327ae-255">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="327ae-256">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="327ae-256">Change the defaults in code</span></span>

<span data-ttu-id="327ae-257">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="327ae-257">*No configuration*</span></span>

<span data-ttu-id="327ae-258">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="327ae-258">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<span data-ttu-id="327ae-259">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="327ae-259">Specify URLs using the:</span></span>

* <span data-ttu-id="327ae-260">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="327ae-260">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="327ae-261">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="327ae-261">`--urls` command-line argument.</span></span>
* <span data-ttu-id="327ae-262">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="327ae-262">`urls` host configuration key.</span></span>
* <span data-ttu-id="327ae-263">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="327ae-263">`UseUrls` extension method.</span></span>

<span data-ttu-id="327ae-264">Para obter mais informações, confira [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="327ae-264">For more information, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="327ae-265">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="327ae-265">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="327ae-266">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="327ae-266">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="327ae-267">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="327ae-267">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="327ae-268"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-268"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="327ae-269">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-269">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="327ae-270">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="327ae-270">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="327ae-271">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="327ae-271">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="327ae-272">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="327ae-272">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="327ae-273">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="327ae-273">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
"Kestrel": {
  "EndPoints": {
    "Http": {
      "Url": "http://localhost:5000"
    },

    "HttpsInlineCertFile": {
      "Url": "https://localhost:5001",
      "Certificate": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    },

    "HttpsInlineCertStore": {
      "Url": "https://localhost:5002",
      "Certificate": {
        "Subject": "<subject; required>",
        "Store": "<certificate store; defaults to My>",
        "Location": "<location; defaults to CurrentUser>",
        "AllowInvalid": "<true or false; defaults to false>"
      }
    },

    "HttpsDefaultCert": {
      "Url": "https://localhost:5003"
    },

    "Https": {
      "Url": "https://*:5004",
      "Certificate": {
      "Path": "<path to .pfx file>",
      "Password": "<certificate password>"
      }
    }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="327ae-274">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="327ae-274">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="327ae-275">Por exemplo, o certificado **Certificados** > **Padrão** pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="327ae-275">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; defaults to My>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="327ae-276">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="327ae-276">Schema notes:</span></span>

* <span data-ttu-id="327ae-277">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="327ae-277">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="327ae-278">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="327ae-278">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="327ae-279">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="327ae-279">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="327ae-280">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="327ae-280">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="327ae-281">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="327ae-281">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="327ae-282">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="327ae-282">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="327ae-283">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="327ae-283">The `Certificate` section is optional.</span></span> <span data-ttu-id="327ae-284">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="327ae-284">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="327ae-285">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="327ae-285">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="327ae-286">A seção `Certificate` é compatível com os certificados de **Caminho**&ndash;**Senha** e **Entidade**&ndash;**Repositório**.</span><span class="sxs-lookup"><span data-stu-id="327ae-286">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="327ae-287">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="327ae-287">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="327ae-288">`options.Configure(context.Configuration.GetSection("Kestrel"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, options => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="327ae-288">`options.Configure(context.Configuration.GetSection("Kestrel"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, options => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

  ```csharp
  options.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  <span data-ttu-id="327ae-289">Você também pode acessar o `KestrelServerOptions.ConfigurationLoader` diretamente para continuar a iteração no carregador existente, como aquela fornecida pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="327ae-289">You can also directly access `KestrelServerOptions.ConfigurationLoader` to keep iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="327ae-290">A seção de configuração de cada ponto de extremidade está disponível nas opções no método `Endpoint` para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="327ae-290">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="327ae-291">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("Kestrel"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="327ae-291">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("Kestrel"))` again with another section.</span></span> <span data-ttu-id="327ae-292">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="327ae-292">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="327ae-293">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="327ae-293">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="327ae-294">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="327ae-294">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="327ae-295">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="327ae-295">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="327ae-296">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="327ae-296">*Change the defaults in code*</span></span>

<span data-ttu-id="327ae-297">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="327ae-297">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="327ae-298">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="327ae-298">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

<span data-ttu-id="327ae-299">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="327ae-299">*Kestrel support for SNI*</span></span>

<span data-ttu-id="327ae-300">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="327ae-300">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="327ae-301">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="327ae-301">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="327ae-302">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="327ae-302">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="327ae-303">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="327ae-303">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="327ae-304">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="327ae-304">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="327ae-305">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="327ae-305">SNI support requires:</span></span>

* <span data-ttu-id="327ae-306">Execução na estrutura de destino `netcoreapp2.1`.</span><span class="sxs-lookup"><span data-stu-id="327ae-306">Running on target framework `netcoreapp2.1`.</span></span> <span data-ttu-id="327ae-307">No `netcoreapp2.0` e no `net461`, o retorno de chamada é invocado, mas o `name` é sempre `null`.</span><span class="sxs-lookup"><span data-stu-id="327ae-307">On `netcoreapp2.0` and `net461`, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="327ae-308">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="327ae-308">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="327ae-309">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-309">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="327ae-310">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="327ae-310">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

::: moniker-end

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="327ae-311">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="327ae-311">Bind to a TCP socket</span></span>

<span data-ttu-id="327ae-312">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="327ae-312">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

::: moniker-end

<span data-ttu-id="327ae-313">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="327ae-313">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="327ae-314">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="327ae-314">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="327ae-315">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="327ae-315">Bind to a Unix socket</span></span>

<span data-ttu-id="327ae-316">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="327ae-316">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.ListenUnixSocket("/tmp/kestrel-test.sock");
            options.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

::: moniker-end

### <a name="port-0"></a><span data-ttu-id="327ae-317">Porta 0</span><span class="sxs-lookup"><span data-stu-id="327ae-317">Port 0</span></span>

<span data-ttu-id="327ae-318">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="327ae-318">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="327ae-319">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="327ae-319">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="327ae-320">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="327ae-320">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="327ae-321">Limitações</span><span class="sxs-lookup"><span data-stu-id="327ae-321">Limitations</span></span>

<span data-ttu-id="327ae-322">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="327ae-322">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="327ae-323">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="327ae-323">`--urls` command-line argument</span></span>
* <span data-ttu-id="327ae-324">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="327ae-324">`urls` host configuration key</span></span>
* <span data-ttu-id="327ae-325">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="327ae-325">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="327ae-326">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-326">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="327ae-327">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="327ae-327">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="327ae-328">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="327ae-328">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="327ae-329">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="327ae-329">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="327ae-330">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="327ae-330">IIS endpoint configuration</span></span>

<span data-ttu-id="327ae-331">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="327ae-331">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="327ae-332">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="327ae-332">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="listenoptionsprotocols"></a><span data-ttu-id="327ae-333">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="327ae-333">ListenOptions.Protocols</span></span>

<span data-ttu-id="327ae-334">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="327ae-334">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="327ae-335">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="327ae-335">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="327ae-336">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="327ae-336">`HttpProtocols` enum value</span></span> | <span data-ttu-id="327ae-337">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="327ae-337">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="327ae-338">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="327ae-338">HTTP/1.1 only.</span></span> <span data-ttu-id="327ae-339">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="327ae-339">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="327ae-340">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="327ae-340">HTTP/2 only.</span></span> <span data-ttu-id="327ae-341">Usado principalmente com TLS.</span><span class="sxs-lookup"><span data-stu-id="327ae-341">Primarily used with TLS.</span></span> <span data-ttu-id="327ae-342">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="327ae-342">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="327ae-343">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="327ae-343">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="327ae-344">Requer uma conexão TLS e [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) para negociar o HTTP/2; caso contrário, o padrão da conexão será HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="327ae-344">Requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection to negotiate HTTP/2; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="327ae-345">O protocolo padrão é HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="327ae-345">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="327ae-346">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="327ae-346">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="327ae-347">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="327ae-347">TLS version 1.2 or later</span></span>
* <span data-ttu-id="327ae-348">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="327ae-348">Renegotiation disabled</span></span>
* <span data-ttu-id="327ae-349">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="327ae-349">Compression disabled</span></span>
* <span data-ttu-id="327ae-350">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="327ae-350">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="327ae-351">ECDHE (Diffie-Hellman de curva elíptica) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="327ae-351">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="327ae-352">DHE (Diffie-Hellman de campo finito) &lbrack;`TLS12`&rbrack; &ndash; mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="327ae-352">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="327ae-353">Pacote de criptografia não autorizado</span><span class="sxs-lookup"><span data-stu-id="327ae-353">Cipher suite not blacklisted</span></span>

<span data-ttu-id="327ae-354">Há suporte para `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 &lbrack;`FIPS186`&rbrack; por padrão.</span><span class="sxs-lookup"><span data-stu-id="327ae-354">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="327ae-355">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="327ae-355">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="327ae-356">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="327ae-356">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="327ae-357">Crie opcionalmente uma implementação do `IConnectionAdapter` para filtrar os handshakes TLS por conexão para codificações específicas:</span><span class="sxs-lookup"><span data-stu-id="327ae-357">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
                listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
            });
        });
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that you don't 
        // wish to support. Alternatively, define and compare 
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher 
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null 
        // indicates that no cipher algorithm supported by Kestrel matches the 
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="327ae-358">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="327ae-358">*Set the protocol from configuration*</span></span>

<span data-ttu-id="327ae-359"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="327ae-359"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="327ae-360">No exemplo *appsettings.json* a seguir, um protocolo de conexão padrão (HTTP/1.1 e HTTP/2) é estabelecido para todos os pontos de extremidade do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="327ae-360">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndPointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="327ae-361">O arquivo de configuração de exemplo a seguir estabelece um protocolo de conexão para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="327ae-361">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "EndPoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="327ae-362">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="327ae-362">Protocols specified in code override values set by configuration.</span></span>

::: moniker-end

## <a name="transport-configuration"></a><span data-ttu-id="327ae-363">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="327ae-363">Transport configuration</span></span>

<span data-ttu-id="327ae-364">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="327ae-364">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="327ae-365">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="327ae-365">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="327ae-366">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="327ae-366">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="327ae-367">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="327ae-367">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="327ae-368">Para projetos do ASP.NET Core 2.1 ou posteriores que usam o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) e exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="327ae-368">For ASP.NET Core 2.1 or later projects that use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and require the use of Libuv:</span></span>

* <span data-ttu-id="327ae-369">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="327ae-369">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv" 
                      Version="<LATEST_VERSION>" />
    ```

* <span data-ttu-id="327ae-370">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="327ae-370">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

    ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseLibuv()
                .UseStartup<Startup>();
    }
    ```

### <a name="url-prefixes"></a><span data-ttu-id="327ae-371">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="327ae-371">URL prefixes</span></span>

<span data-ttu-id="327ae-372">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="327ae-372">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="327ae-373">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="327ae-373">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="327ae-374">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="327ae-374">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="327ae-375">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="327ae-375">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="327ae-376">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="327ae-376">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="327ae-377">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="327ae-377">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="327ae-378">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="327ae-378">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="327ae-379">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="327ae-379">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="327ae-380">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="327ae-380">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="327ae-381">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="327ae-381">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="327ae-382">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="327ae-382">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="327ae-383">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="327ae-383">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="327ae-384">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="327ae-384">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="327ae-385">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="327ae-385">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="327ae-386">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="327ae-386">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="327ae-387">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="327ae-387">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="327ae-388">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="327ae-388">Host filtering</span></span>

<span data-ttu-id="327ae-389">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="327ae-389">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="327ae-390">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="327ae-390">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="327ae-391">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="327ae-391">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="327ae-392">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="327ae-392">`Host` headers aren't validated.</span></span>

<span data-ttu-id="327ae-393">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="327ae-393">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="327ae-394">Middleware de Filtragem de Host é fornecido pelo pacote [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), que está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="327ae-394">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="327ae-395">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="327ae-395">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="327ae-396">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="327ae-396">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="327ae-397">Para habilitar o middleware, defina uma chave `AllowedHosts` em *appSettings. JSON*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="327ae-397">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="327ae-398">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="327ae-398">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="327ae-399">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="327ae-399">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="327ae-400">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="327ae-400">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="327ae-401">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="327ae-401">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="327ae-402">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="327ae-402">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="327ae-403">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="327ae-403">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="327ae-404">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="327ae-404">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="327ae-405">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="327ae-405">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="327ae-406">Código-fonte do kestrel</span><span class="sxs-lookup"><span data-stu-id="327ae-406">Kestrel source code</span></span>](https://github.com/aspnet/KestrelHttpServer)
* [<span data-ttu-id="327ae-407">RFC 7230: sintaxe e roteamento da mensagem (Seção 5.4: host)</span><span class="sxs-lookup"><span data-stu-id="327ae-407">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
