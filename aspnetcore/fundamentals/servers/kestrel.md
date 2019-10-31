---
title: Implementação do servidor Web Kestrel no ASP.NET Core
author: guardrex
description: Saiba mais sobre o Kestrel, o servidor Web multiplataforma do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: beaf6ac49359adfdc2dc24221eab04cc853646a9
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143446"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="daf75-103">Implementação do servidor Web Kestrel no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daf75-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="daf75-104">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen haltr](https://twitter.com/halter73)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="daf75-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="daf75-105">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="daf75-106">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="daf75-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="daf75-107">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="daf75-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="daf75-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="daf75-108">HTTPS</span></span>
* <span data-ttu-id="daf75-109">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="daf75-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="daf75-110">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="daf75-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="daf75-111">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="daf75-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="daf75-112">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="daf75-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="daf75-113">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="daf75-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="daf75-114">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daf75-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="daf75-115">Compatibilidade com HTTP/2</span><span class="sxs-lookup"><span data-stu-id="daf75-115">HTTP/2 support</span></span>

<span data-ttu-id="daf75-116">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="daf75-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="daf75-117">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="daf75-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="daf75-118">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="daf75-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="daf75-119">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="daf75-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="daf75-120">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="daf75-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="daf75-121">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="daf75-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="daf75-122">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="daf75-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="daf75-123">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="daf75-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="daf75-124">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="daf75-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="daf75-125">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="daf75-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="daf75-126">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="daf75-127">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="daf75-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="daf75-128">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="daf75-129">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="daf75-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="daf75-130">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="daf75-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="daf75-131">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="daf75-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="daf75-132">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="daf75-133">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="daf75-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="daf75-135">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="daf75-135">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="daf75-137">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="daf75-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="daf75-138">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="daf75-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="daf75-139">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="daf75-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="daf75-140">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="daf75-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="daf75-141">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="daf75-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="daf75-142">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="daf75-142">A reverse proxy:</span></span>

* <span data-ttu-id="daf75-143">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="daf75-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="daf75-144">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="daf75-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="daf75-145">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="daf75-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="daf75-146">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="daf75-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="daf75-147">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="daf75-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="daf75-148">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="daf75-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="daf75-149">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daf75-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="daf75-150">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="daf75-151">Em *Program.cs*, o código de modelo chama <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="daf75-151">In *Program.cs*, the template code calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="daf75-152">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte as seções *configurar um host* e *as configurações do construtor padrão* de <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="daf75-152">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="daf75-153">Para fornecer configuração adicional depois de chamar `CreateDefaultBuilder` e `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="daf75-153">To provide additional configuration after calling `CreateDefaultBuilder` and `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="daf75-154">Se o aplicativo não chamar `CreateDefaultBuilder` para configurar o host, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **antes** de chamar `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="daf75-154">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new HostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseIISIntegration()
            .UseStartup<Startup>();
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="daf75-155">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="daf75-155">Kestrel options</span></span>

<span data-ttu-id="daf75-156">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="daf75-156">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="daf75-157">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="daf75-157">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="daf75-158">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="daf75-158">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="daf75-159">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="daf75-159">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="daf75-160">As opções de Kestrel, que são C# configuradas no código nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-160">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="daf75-161">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="daf75-161">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

<span data-ttu-id="daf75-162">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="daf75-162">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="daf75-163">Configurar Kestrel no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="daf75-163">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="daf75-164">Injetar uma instância de `IConfiguration` na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="daf75-164">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="daf75-165">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à propriedade `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="daf75-165">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="daf75-166">No `Startup.ConfigureServices`, carregue a seção `Kestrel` da configuração na configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-166">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="daf75-167">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="daf75-167">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="daf75-168">No *Program.cs*, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="daf75-168">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="daf75-169">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-169">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="daf75-170">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="daf75-170">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="daf75-171">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="daf75-171">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="daf75-172">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="daf75-172">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="daf75-173">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="daf75-173">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="daf75-174">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-174">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="daf75-175">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="daf75-175">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="daf75-176">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="daf75-176">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="daf75-177">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-177">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="daf75-178">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-178">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="daf75-179">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="daf75-179">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="daf75-180">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="daf75-180">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="daf75-181">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="daf75-181">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="daf75-182">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="daf75-182">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="daf75-183">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-183">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="daf75-184">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="daf75-184">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="daf75-185">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="daf75-185">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="daf75-186">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-186">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="daf75-187">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="daf75-187">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="daf75-188">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="daf75-188">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="daf75-189">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="daf75-189">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="daf75-190">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="daf75-190">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="daf75-191">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="daf75-191">A minimum rate also applies to the response.</span></span> <span data-ttu-id="daf75-192">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="daf75-192">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="daf75-193">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="daf75-193">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="daf75-194">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="daf75-194">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="daf75-195">O <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenciado no exemplo anterior não está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é geralmente compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="daf75-195">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="daf75-196">No entanto, o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ainda está presente em `HttpContext.Features` para solicitações HTTP/2, pois o limite de taxa de leitura ainda pode ser *desabilitado totalmente* em cada solicitação, definindo `IHttpMinRequestBodyDataRateFeature.MinDataRate` para `null` mesmo em uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-196">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="daf75-197">Tentar ler `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou tentar defini-lo como um valor diferente de `null` resultará na geração de um `NotSupportedException` devido a uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-197">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="daf75-198">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-198">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="daf75-199">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-199">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="daf75-200">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-200">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="daf75-201">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="daf75-201">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="daf75-202">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="daf75-202">Maximum streams per connection</span></span>

<span data-ttu-id="daf75-203">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-203">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="daf75-204">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="daf75-204">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="daf75-205">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="daf75-205">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="daf75-206">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="daf75-206">Header table size</span></span>

<span data-ttu-id="daf75-207">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-207">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="daf75-208">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="daf75-208">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="daf75-209">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="daf75-209">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="daf75-210">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="daf75-210">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="daf75-211">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="daf75-211">Maximum frame size</span></span>

<span data-ttu-id="daf75-212">`Http2.MaxFrameSize` indica o tamanho máximo permitido de um conteúdo de quadro de conexão HTTP/2 recebido ou enviado pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="daf75-212">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="daf75-213">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="daf75-213">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="daf75-214">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="daf75-214">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="daf75-215">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-215">Maximum request header size</span></span>

<span data-ttu-id="daf75-216">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-216">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="daf75-217">Esse limite se aplica tanto ao nome quanto ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="daf75-217">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="daf75-218">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="daf75-218">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="daf75-219">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="daf75-219">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="daf75-220">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="daf75-220">Initial connection window size</span></span>

<span data-ttu-id="daf75-221">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="daf75-221">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="daf75-222">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="daf75-222">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="daf75-223">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="daf75-223">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="daf75-224">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="daf75-224">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="daf75-225">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="daf75-225">Initial stream window size</span></span>

<span data-ttu-id="daf75-226">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="daf75-226">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="daf75-227">As solicitações também são limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="daf75-227">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="daf75-228">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="daf75-228">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="daf75-229">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="daf75-229">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="daf75-230">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="daf75-230">Synchronous IO</span></span>

<span data-ttu-id="daf75-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a E/S síncrona é permitida para a solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="daf75-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="daf75-232">O valor padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="daf75-232">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="daf75-233">Um grande número de operações de E/S síncronas de bloqueio pode levar à privação de pool de thread, o que faz com que o aplicativo não responda.</span><span class="sxs-lookup"><span data-stu-id="daf75-233">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="daf75-234">Habilite `AllowSynchronousIO` somente ao usar uma biblioteca em que não há suporte para E/S assíncrona.</span><span class="sxs-lookup"><span data-stu-id="daf75-234">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="daf75-235">O exemplo a seguir ativa a E/S síncrona:</span><span class="sxs-lookup"><span data-stu-id="daf75-235">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="daf75-236">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="daf75-236">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="daf75-237">Configuração do ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="daf75-237">Endpoint configuration</span></span>

<span data-ttu-id="daf75-238">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="daf75-238">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="daf75-239">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="daf75-239">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="daf75-240">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="daf75-240">Specify URLs using the:</span></span>

* <span data-ttu-id="daf75-241">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="daf75-241">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="daf75-242">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-242">`--urls` command-line argument.</span></span>
* <span data-ttu-id="daf75-243">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-243">`urls` host configuration key.</span></span>
* <span data-ttu-id="daf75-244">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-244">`UseUrls` extension method.</span></span>

<span data-ttu-id="daf75-245">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="daf75-245">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="daf75-246">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000; http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="daf75-246">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="daf75-247">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="daf75-247">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="daf75-248">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="daf75-248">A development certificate is created:</span></span>

* <span data-ttu-id="daf75-249">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="daf75-249">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="daf75-250">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-250">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="daf75-251">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="daf75-251">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="daf75-252">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="daf75-252">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="daf75-253">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-253">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="daf75-254">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="daf75-254">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="daf75-255">configuração do `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="daf75-255">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="daf75-256">ConfigureEndpointDefaults (ação\<ListenerOptions >)</span><span class="sxs-lookup"><span data-stu-id="daf75-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="daf75-257">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-257">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="daf75-258">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="daf75-258">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="daf75-259">ConfigureHttpsDefaults (ação\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="daf75-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="daf75-260">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="daf75-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="daf75-261">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="daf75-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

### <a name="configureiconfiguration"></a><span data-ttu-id="daf75-262">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="daf75-262">Configure(IConfiguration)</span></span>

<span data-ttu-id="daf75-263">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="daf75-263">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="daf75-264">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-264">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="daf75-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="daf75-265">ListenOptions.UseHttps</span></span>

<span data-ttu-id="daf75-266">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="daf75-266">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="daf75-267">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="daf75-267">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="daf75-268">`UseHttps` &ndash; Configure o Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-268">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="daf75-269">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="daf75-269">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="daf75-270">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="daf75-270">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="daf75-271">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daf75-271">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="daf75-272">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="daf75-272">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="daf75-273">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="daf75-273">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="daf75-274">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="daf75-274">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="daf75-275">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="daf75-275">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="daf75-276">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-276">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="daf75-277">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="daf75-277">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="daf75-278">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="daf75-278">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="daf75-279">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="daf75-279">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="daf75-280">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="daf75-280">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="daf75-281">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="daf75-281">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="daf75-282">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-282">Supported configurations described next:</span></span>

* <span data-ttu-id="daf75-283">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="daf75-283">No configuration</span></span>
* <span data-ttu-id="daf75-284">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="daf75-284">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="daf75-285">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="daf75-285">Change the defaults in code</span></span>

<span data-ttu-id="daf75-286">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-286">*No configuration*</span></span>

<span data-ttu-id="daf75-287">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="daf75-287">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="daf75-288">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-288">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="daf75-289">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-289">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="daf75-290">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-290">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="daf75-291">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="daf75-291">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="daf75-292">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-292">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="daf75-293">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="daf75-293">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="daf75-294">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="daf75-294">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
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
          "Store": "<certificate store; required>",
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

<span data-ttu-id="daf75-295">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="daf75-295">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="daf75-296">Por exemplo, o certificado **Certificados** > **Padrão** pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="daf75-296">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="daf75-297">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="daf75-297">Schema notes:</span></span>

* <span data-ttu-id="daf75-298">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="daf75-298">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="daf75-299">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="daf75-299">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="daf75-300">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="daf75-300">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="daf75-301">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="daf75-301">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="daf75-302">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="daf75-302">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="daf75-303">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-303">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="daf75-304">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="daf75-304">The `Certificate` section is optional.</span></span> <span data-ttu-id="daf75-305">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="daf75-305">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="daf75-306">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="daf75-306">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="daf75-307">A seção `Certificate` é compatível com os certificados de **Caminho**&ndash;**Senha** e **Entidade**&ndash;**Repositório**.</span><span class="sxs-lookup"><span data-stu-id="daf75-307">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="daf75-308">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="daf75-308">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="daf75-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="daf75-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="daf75-310">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="daf75-310">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="daf75-311">A seção de configuração para cada ponto de extremidade está disponível nas opções no método `Endpoint` para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="daf75-311">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="daf75-312">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="daf75-312">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="daf75-313">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="daf75-313">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="daf75-314">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="daf75-314">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="daf75-315">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="daf75-315">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="daf75-316">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-316">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="daf75-317">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="daf75-317">*Change the defaults in code*</span></span>

<span data-ttu-id="daf75-318">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="daf75-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="daf75-319">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="daf75-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="daf75-320">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="daf75-320">*Kestrel support for SNI*</span></span>

<span data-ttu-id="daf75-321">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="daf75-321">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="daf75-322">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="daf75-322">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="daf75-323">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-323">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="daf75-324">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="daf75-324">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="daf75-325">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="daf75-325">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="daf75-326">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="daf75-326">SNI support requires:</span></span>

* <span data-ttu-id="daf75-327">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="daf75-327">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="daf75-328">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` é sempre `null`.</span><span class="sxs-lookup"><span data-stu-id="daf75-328">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="daf75-329">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-329">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="daf75-330">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-330">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="daf75-331">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="daf75-331">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
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

### <a name="connection-logging"></a><span data-ttu-id="daf75-332">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="daf75-332">Connection logging</span></span>

<span data-ttu-id="daf75-333">Chame <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="daf75-333">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="daf75-334">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="daf75-334">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="daf75-335">Se `UseConnectionLogging` for colocado antes `UseHttps`, o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="daf75-335">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="daf75-336">Se `UseConnectionLogging` for colocado após `UseHttps`, o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="daf75-336">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="daf75-337">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="daf75-337">Bind to a TCP socket</span></span>

<span data-ttu-id="daf75-338">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="daf75-338">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="daf75-339">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="daf75-339">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="daf75-340">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="daf75-340">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="daf75-341">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="daf75-341">Bind to a Unix socket</span></span>

<span data-ttu-id="daf75-342">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="daf75-342">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="daf75-343">Porta 0</span><span class="sxs-lookup"><span data-stu-id="daf75-343">Port 0</span></span>

<span data-ttu-id="daf75-344">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="daf75-344">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="daf75-345">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="daf75-345">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="daf75-346">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="daf75-346">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="daf75-347">Limitações</span><span class="sxs-lookup"><span data-stu-id="daf75-347">Limitations</span></span>

<span data-ttu-id="daf75-348">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="daf75-348">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="daf75-349">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="daf75-349">`--urls` command-line argument</span></span>
* <span data-ttu-id="daf75-350">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="daf75-350">`urls` host configuration key</span></span>
* <span data-ttu-id="daf75-351">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="daf75-351">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="daf75-352">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-352">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="daf75-353">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="daf75-353">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="daf75-354">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="daf75-354">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="daf75-355">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-355">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="daf75-356">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="daf75-356">IIS endpoint configuration</span></span>

<span data-ttu-id="daf75-357">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-357">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="daf75-358">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="daf75-358">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="daf75-359">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="daf75-359">ListenOptions.Protocols</span></span>

<span data-ttu-id="daf75-360">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="daf75-360">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="daf75-361">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="daf75-361">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="daf75-362">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="daf75-362">`HttpProtocols` enum value</span></span> | <span data-ttu-id="daf75-363">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="daf75-363">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="daf75-364">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="daf75-364">HTTP/1.1 only.</span></span> <span data-ttu-id="daf75-365">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-365">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="daf75-366">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="daf75-366">HTTP/2 only.</span></span> <span data-ttu-id="daf75-367">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="daf75-367">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="daf75-368">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-368">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="daf75-369">O HTTP/2 requer que o cliente selecione HTTP/2 no handshake de [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="daf75-369">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="daf75-370">O valor padrão `ListenOptions.Protocols` para qualquer ponto de extremidade é `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="daf75-370">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="daf75-371">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="daf75-371">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="daf75-372">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="daf75-372">TLS version 1.2 or later</span></span>
* <span data-ttu-id="daf75-373">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="daf75-373">Renegotiation disabled</span></span>
* <span data-ttu-id="daf75-374">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="daf75-374">Compression disabled</span></span>
* <span data-ttu-id="daf75-375">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="daf75-375">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="daf75-376">ECDHE (Diffie-Hellman de curva elíptica) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="daf75-376">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="daf75-377">DHE (Diffie-Hellman de campo finito) &lbrack;`TLS12`&rbrack; &ndash; mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="daf75-377">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="daf75-378">Pacote de criptografia não autorizado</span><span class="sxs-lookup"><span data-stu-id="daf75-378">Cipher suite not blacklisted</span></span>

<span data-ttu-id="daf75-379">Há suporte para `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 &lbrack;`FIPS186`&rbrack; por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-379">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="daf75-380">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="daf75-380">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="daf75-381">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="daf75-381">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="daf75-382">Use o middleware de conexão para filtrar os Handshakes TLS em uma base por conexão para codificações específicas, se necessário.</span><span class="sxs-lookup"><span data-stu-id="daf75-382">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="daf75-383">O exemplo a seguir gera <xref:System.NotSupportedException> para qualquer algoritmo de codificação para o qual o aplicativo não dá suporte.</span><span class="sxs-lookup"><span data-stu-id="daf75-383">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="daf75-384">Como alternativa, defina e compare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) com uma lista de pacotes de codificação aceitáveis.</span><span class="sxs-lookup"><span data-stu-id="daf75-384">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="daf75-385">Nenhuma criptografia é usada com um algoritmo de codificação [CipherAlgorithmType. NULL](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="daf75-385">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="daf75-386">A filtragem de conexão também pode ser configurada por meio de um lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="daf75-386">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="daf75-387">No Linux, <xref:System.Net.Security.CipherSuitesPolicy> pode ser usado para filtrar os Handshakes de TLS em uma base por conexão:</span><span class="sxs-lookup"><span data-stu-id="daf75-387">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="daf75-388">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-388">*Set the protocol from configuration*</span></span>

<span data-ttu-id="daf75-389">`CreateDefaultBuilder` chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-389">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="daf75-390">O exemplo *appSettings. JSON* a seguir estabelece o http/1.1 como o protocolo de conexão padrão para todos os pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="daf75-390">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="daf75-391">O exemplo *appSettings. JSON* a seguir estabelece o protocolo de conexão HTTP/1.1 para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="daf75-391">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="daf75-392">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-392">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="daf75-393">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="daf75-393">Transport configuration</span></span>

<span data-ttu-id="daf75-394">Para projetos que exigem o uso de Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="daf75-394">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="daf75-395">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="daf75-395">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="daf75-396">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> no `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="daf75-396">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="daf75-397">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="daf75-397">URL prefixes</span></span>

<span data-ttu-id="daf75-398">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="daf75-398">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="daf75-399">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="daf75-399">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="daf75-400">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-400">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="daf75-401">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-401">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="daf75-402">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="daf75-402">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="daf75-403">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-403">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="daf75-404">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="daf75-404">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="daf75-405">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-405">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="daf75-406">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="daf75-406">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="daf75-407">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="daf75-407">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="daf75-408">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="daf75-408">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="daf75-409">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="daf75-409">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="daf75-410">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-410">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="daf75-411">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="daf75-411">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="daf75-412">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="daf75-412">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="daf75-413">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="daf75-413">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="daf75-414">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="daf75-414">Host filtering</span></span>

<span data-ttu-id="daf75-415">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="daf75-415">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="daf75-416">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="daf75-416">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="daf75-417">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="daf75-417">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="daf75-418">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="daf75-418">`Host` headers aren't validated.</span></span>

<span data-ttu-id="daf75-419">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="daf75-419">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="daf75-420">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que é fornecido implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="daf75-420">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="daf75-421">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="daf75-421">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="daf75-422">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-422">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="daf75-423">Para habilitar o middleware, defina uma chave `AllowedHosts` em *appSettings. JSON*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="daf75-423">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="daf75-424">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="daf75-424">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="daf75-425">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="daf75-425">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="daf75-426">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="daf75-426">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="daf75-427">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="daf75-427">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="daf75-428">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="daf75-428">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="daf75-429">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="daf75-429">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="daf75-430">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="daf75-430">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="daf75-431">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-431">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="daf75-432">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="daf75-432">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="daf75-433">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="daf75-433">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="daf75-434">HTTPS</span><span class="sxs-lookup"><span data-stu-id="daf75-434">HTTPS</span></span>
* <span data-ttu-id="daf75-435">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="daf75-435">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="daf75-436">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="daf75-436">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="daf75-437">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="daf75-437">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="daf75-438">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="daf75-438">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="daf75-439">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="daf75-439">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="daf75-440">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daf75-440">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="daf75-441">Compatibilidade com HTTP/2</span><span class="sxs-lookup"><span data-stu-id="daf75-441">HTTP/2 support</span></span>

<span data-ttu-id="daf75-442">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="daf75-442">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="daf75-443">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="daf75-443">Operating system&dagger;</span></span>
  * <span data-ttu-id="daf75-444">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="daf75-444">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="daf75-445">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="daf75-445">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="daf75-446">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="daf75-446">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="daf75-447">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="daf75-447">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="daf75-448">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="daf75-448">TLS 1.2 or later connection</span></span>

<span data-ttu-id="daf75-449">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="daf75-449">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="daf75-450">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="daf75-450">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="daf75-451">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="daf75-451">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="daf75-452">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-452">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="daf75-453">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="daf75-453">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="daf75-454">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-454">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="daf75-455">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="daf75-455">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="daf75-456">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="daf75-456">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="daf75-457">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="daf75-457">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="daf75-458">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-458">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="daf75-459">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="daf75-459">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="daf75-461">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="daf75-461">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="daf75-463">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="daf75-463">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="daf75-464">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="daf75-464">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="daf75-465">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="daf75-465">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="daf75-466">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="daf75-466">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="daf75-467">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="daf75-467">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="daf75-468">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="daf75-468">A reverse proxy:</span></span>

* <span data-ttu-id="daf75-469">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="daf75-469">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="daf75-470">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="daf75-470">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="daf75-471">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="daf75-471">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="daf75-472">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="daf75-472">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="daf75-473">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="daf75-473">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="daf75-474">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="daf75-474">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="daf75-475">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daf75-475">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="daf75-476">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="daf75-476">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="daf75-477">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-477">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="daf75-478">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="daf75-478">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="daf75-479">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="daf75-479">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="daf75-480">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="daf75-480">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="daf75-481">Se o aplicativo não chamar `CreateDefaultBuilder` para configurar o host, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **antes** de chamar `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="daf75-481">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="daf75-482">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="daf75-482">Kestrel options</span></span>

<span data-ttu-id="daf75-483">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="daf75-483">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="daf75-484">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="daf75-484">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="daf75-485">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="daf75-485">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="daf75-486">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="daf75-486">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="daf75-487">As opções de Kestrel, que são C# configuradas no código nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-487">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="daf75-488">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="daf75-488">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="daf75-489">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="daf75-489">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="daf75-490">Configurar Kestrel no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="daf75-490">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="daf75-491">Injetar uma instância de `IConfiguration` na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="daf75-491">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="daf75-492">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à propriedade `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="daf75-492">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="daf75-493">No `Startup.ConfigureServices`, carregue a seção `Kestrel` da configuração na configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-493">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="daf75-494">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="daf75-494">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="daf75-495">No *Program.cs*, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="daf75-495">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="daf75-496">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-496">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="daf75-497">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="daf75-497">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="daf75-498">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="daf75-498">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="daf75-499">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="daf75-499">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="daf75-500">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="daf75-500">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="daf75-501">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-501">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="daf75-502">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="daf75-502">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="daf75-503">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="daf75-503">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="daf75-504">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-504">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="daf75-505">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-505">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="daf75-506">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="daf75-506">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="daf75-507">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="daf75-507">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="daf75-508">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="daf75-508">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="daf75-509">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="daf75-509">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="daf75-510">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-510">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="daf75-511">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="daf75-511">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="daf75-512">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="daf75-512">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="daf75-513">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-513">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="daf75-514">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="daf75-514">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="daf75-515">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="daf75-515">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="daf75-516">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="daf75-516">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="daf75-517">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="daf75-517">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="daf75-518">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="daf75-518">A minimum rate also applies to the response.</span></span> <span data-ttu-id="daf75-519">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="daf75-519">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="daf75-520">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="daf75-520">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="daf75-521">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="daf75-521">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="daf75-522">Nenhum desses recursos de taxa referenciados no exemplo anterior está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="daf75-522">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="daf75-523">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-523">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="daf75-524">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-524">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="daf75-525">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-525">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="daf75-526">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="daf75-526">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="daf75-527">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="daf75-527">Maximum streams per connection</span></span>

<span data-ttu-id="daf75-528">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-528">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="daf75-529">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="daf75-529">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="daf75-530">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="daf75-530">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="daf75-531">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="daf75-531">Header table size</span></span>

<span data-ttu-id="daf75-532">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-532">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="daf75-533">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="daf75-533">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="daf75-534">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="daf75-534">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="daf75-535">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="daf75-535">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="daf75-536">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="daf75-536">Maximum frame size</span></span>

<span data-ttu-id="daf75-537">O `Http2.MaxFrameSize` indica o tamanho máximo do payload do quadro da conexão HTTP/2 a ser recebido.</span><span class="sxs-lookup"><span data-stu-id="daf75-537">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="daf75-538">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="daf75-538">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="daf75-539">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="daf75-539">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="daf75-540">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-540">Maximum request header size</span></span>

<span data-ttu-id="daf75-541">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-541">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="daf75-542">Esse limite se aplica ao nome e ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="daf75-542">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="daf75-543">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="daf75-543">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="daf75-544">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="daf75-544">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="daf75-545">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="daf75-545">Initial connection window size</span></span>

<span data-ttu-id="daf75-546">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="daf75-546">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="daf75-547">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="daf75-547">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="daf75-548">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="daf75-548">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="daf75-549">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="daf75-549">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="daf75-550">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="daf75-550">Initial stream window size</span></span>

<span data-ttu-id="daf75-551">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="daf75-551">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="daf75-552">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="daf75-552">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="daf75-553">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="daf75-553">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="daf75-554">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="daf75-554">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="daf75-555">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="daf75-555">Synchronous IO</span></span>

<span data-ttu-id="daf75-556"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a E/S síncrona é permitida para a solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="daf75-556"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="daf75-557">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="daf75-557">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="daf75-558">Um grande número de operações de E/S síncronas de bloqueio pode levar à privação de pool de thread, o que faz com que o aplicativo não responda.</span><span class="sxs-lookup"><span data-stu-id="daf75-558">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="daf75-559">Habilite `AllowSynchronousIO` somente ao usar uma biblioteca em que não há suporte para E/S assíncrona.</span><span class="sxs-lookup"><span data-stu-id="daf75-559">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="daf75-560">O exemplo a seguir ativa a E/S síncrona:</span><span class="sxs-lookup"><span data-stu-id="daf75-560">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="daf75-561">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="daf75-561">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="daf75-562">Configuração do ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="daf75-562">Endpoint configuration</span></span>

<span data-ttu-id="daf75-563">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="daf75-563">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="daf75-564">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="daf75-564">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="daf75-565">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="daf75-565">Specify URLs using the:</span></span>

* <span data-ttu-id="daf75-566">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="daf75-566">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="daf75-567">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-567">`--urls` command-line argument.</span></span>
* <span data-ttu-id="daf75-568">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-568">`urls` host configuration key.</span></span>
* <span data-ttu-id="daf75-569">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-569">`UseUrls` extension method.</span></span>

<span data-ttu-id="daf75-570">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="daf75-570">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="daf75-571">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000; http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="daf75-571">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="daf75-572">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="daf75-572">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="daf75-573">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="daf75-573">A development certificate is created:</span></span>

* <span data-ttu-id="daf75-574">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="daf75-574">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="daf75-575">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-575">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="daf75-576">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="daf75-576">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="daf75-577">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="daf75-577">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="daf75-578">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-578">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="daf75-579">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="daf75-579">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="daf75-580">configuração do `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="daf75-580">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="daf75-581">ConfigureEndpointDefaults (ação\<ListenerOptions >)</span><span class="sxs-lookup"><span data-stu-id="daf75-581">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="daf75-582">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-582">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="daf75-583">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="daf75-583">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="daf75-584">ConfigureHttpsDefaults (ação\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="daf75-584">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="daf75-585">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="daf75-585">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="daf75-586">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="daf75-586">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a><span data-ttu-id="daf75-587">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="daf75-587">Configure(IConfiguration)</span></span>

<span data-ttu-id="daf75-588">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="daf75-588">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="daf75-589">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-589">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="daf75-590">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="daf75-590">ListenOptions.UseHttps</span></span>

<span data-ttu-id="daf75-591">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="daf75-591">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="daf75-592">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="daf75-592">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="daf75-593">`UseHttps` &ndash; Configure o Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-593">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="daf75-594">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="daf75-594">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="daf75-595">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="daf75-595">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="daf75-596">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daf75-596">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="daf75-597">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="daf75-597">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="daf75-598">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="daf75-598">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="daf75-599">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="daf75-599">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="daf75-600">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="daf75-600">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="daf75-601">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-601">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="daf75-602">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="daf75-602">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="daf75-603">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="daf75-603">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="daf75-604">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="daf75-604">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="daf75-605">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="daf75-605">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="daf75-606">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="daf75-606">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="daf75-607">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-607">Supported configurations described next:</span></span>

* <span data-ttu-id="daf75-608">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="daf75-608">No configuration</span></span>
* <span data-ttu-id="daf75-609">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="daf75-609">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="daf75-610">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="daf75-610">Change the defaults in code</span></span>

<span data-ttu-id="daf75-611">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-611">*No configuration*</span></span>

<span data-ttu-id="daf75-612">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="daf75-612">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="daf75-613">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-613">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="daf75-614">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-614">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="daf75-615">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-615">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="daf75-616">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="daf75-616">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="daf75-617">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-617">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="daf75-618">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="daf75-618">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="daf75-619">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="daf75-619">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
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
          "Store": "<certificate store; required>",
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

<span data-ttu-id="daf75-620">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="daf75-620">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="daf75-621">Por exemplo, o certificado **Certificados** > **Padrão** pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="daf75-621">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="daf75-622">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="daf75-622">Schema notes:</span></span>

* <span data-ttu-id="daf75-623">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="daf75-623">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="daf75-624">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="daf75-624">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="daf75-625">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="daf75-625">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="daf75-626">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="daf75-626">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="daf75-627">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="daf75-627">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="daf75-628">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-628">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="daf75-629">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="daf75-629">The `Certificate` section is optional.</span></span> <span data-ttu-id="daf75-630">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="daf75-630">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="daf75-631">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="daf75-631">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="daf75-632">A seção `Certificate` é compatível com os certificados de **Caminho**&ndash;**Senha** e **Entidade**&ndash;**Repositório**.</span><span class="sxs-lookup"><span data-stu-id="daf75-632">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="daf75-633">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="daf75-633">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="daf75-634">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="daf75-634">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="daf75-635">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="daf75-635">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="daf75-636">A seção de configuração para cada ponto de extremidade está disponível nas opções no método `Endpoint` para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="daf75-636">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="daf75-637">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="daf75-637">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="daf75-638">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="daf75-638">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="daf75-639">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="daf75-639">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="daf75-640">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="daf75-640">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="daf75-641">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-641">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="daf75-642">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="daf75-642">*Change the defaults in code*</span></span>

<span data-ttu-id="daf75-643">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="daf75-643">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="daf75-644">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="daf75-644">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="daf75-645">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="daf75-645">*Kestrel support for SNI*</span></span>

<span data-ttu-id="daf75-646">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="daf75-646">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="daf75-647">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="daf75-647">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="daf75-648">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-648">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="daf75-649">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="daf75-649">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="daf75-650">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="daf75-650">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="daf75-651">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="daf75-651">SNI support requires:</span></span>

* <span data-ttu-id="daf75-652">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="daf75-652">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="daf75-653">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` é sempre `null`.</span><span class="sxs-lookup"><span data-stu-id="daf75-653">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="daf75-654">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-654">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="daf75-655">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-655">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="daf75-656">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="daf75-656">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
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

### <a name="connection-logging"></a><span data-ttu-id="daf75-657">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="daf75-657">Connection logging</span></span>

<span data-ttu-id="daf75-658">Chame <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="daf75-658">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="daf75-659">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="daf75-659">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="daf75-660">Se `UseConnectionLogging` for colocado antes `UseHttps`, o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="daf75-660">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="daf75-661">Se `UseConnectionLogging` for colocado após `UseHttps`, o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="daf75-661">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="daf75-662">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="daf75-662">Bind to a TCP socket</span></span>

<span data-ttu-id="daf75-663">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="daf75-663">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="daf75-664">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="daf75-664">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="daf75-665">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="daf75-665">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="daf75-666">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="daf75-666">Bind to a Unix socket</span></span>

<span data-ttu-id="daf75-667">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="daf75-667">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="daf75-668">Porta 0</span><span class="sxs-lookup"><span data-stu-id="daf75-668">Port 0</span></span>

<span data-ttu-id="daf75-669">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="daf75-669">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="daf75-670">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="daf75-670">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="daf75-671">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="daf75-671">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="daf75-672">Limitações</span><span class="sxs-lookup"><span data-stu-id="daf75-672">Limitations</span></span>

<span data-ttu-id="daf75-673">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="daf75-673">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="daf75-674">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="daf75-674">`--urls` command-line argument</span></span>
* <span data-ttu-id="daf75-675">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="daf75-675">`urls` host configuration key</span></span>
* <span data-ttu-id="daf75-676">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="daf75-676">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="daf75-677">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-677">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="daf75-678">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="daf75-678">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="daf75-679">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="daf75-679">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="daf75-680">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-680">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="daf75-681">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="daf75-681">IIS endpoint configuration</span></span>

<span data-ttu-id="daf75-682">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-682">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="daf75-683">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="daf75-683">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="daf75-684">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="daf75-684">ListenOptions.Protocols</span></span>

<span data-ttu-id="daf75-685">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="daf75-685">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="daf75-686">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="daf75-686">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="daf75-687">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="daf75-687">`HttpProtocols` enum value</span></span> | <span data-ttu-id="daf75-688">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="daf75-688">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="daf75-689">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="daf75-689">HTTP/1.1 only.</span></span> <span data-ttu-id="daf75-690">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-690">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="daf75-691">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="daf75-691">HTTP/2 only.</span></span> <span data-ttu-id="daf75-692">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="daf75-692">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="daf75-693">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="daf75-693">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="daf75-694">O HTTP/2 requer uma conexão TLS e [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) ; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="daf75-694">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="daf75-695">O protocolo padrão é HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="daf75-695">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="daf75-696">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="daf75-696">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="daf75-697">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="daf75-697">TLS version 1.2 or later</span></span>
* <span data-ttu-id="daf75-698">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="daf75-698">Renegotiation disabled</span></span>
* <span data-ttu-id="daf75-699">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="daf75-699">Compression disabled</span></span>
* <span data-ttu-id="daf75-700">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="daf75-700">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="daf75-701">ECDHE (Diffie-Hellman de curva elíptica) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; mínimo de 224 bits</span><span class="sxs-lookup"><span data-stu-id="daf75-701">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="daf75-702">DHE (Diffie-Hellman de campo finito) &lbrack;`TLS12`&rbrack; &ndash; mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="daf75-702">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="daf75-703">Pacote de criptografia não autorizado</span><span class="sxs-lookup"><span data-stu-id="daf75-703">Cipher suite not blacklisted</span></span>

<span data-ttu-id="daf75-704">Há suporte para `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 &lbrack;`FIPS186`&rbrack; por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-704">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="daf75-705">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="daf75-705">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="daf75-706">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="daf75-706">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="daf75-707">Crie opcionalmente uma implementação do `IConnectionAdapter` para filtrar os handshakes TLS por conexão para codificações específicas:</span><span class="sxs-lookup"><span data-stu-id="daf75-707">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
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

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
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

<span data-ttu-id="daf75-708">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-708">*Set the protocol from configuration*</span></span>

<span data-ttu-id="daf75-709"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-709"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="daf75-710">No exemplo *appsettings.json* a seguir, um protocolo de conexão padrão (HTTP/1.1 e HTTP/2) é estabelecido para todos os pontos de extremidade do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="daf75-710">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="daf75-711">O arquivo de configuração de exemplo a seguir estabelece um protocolo de conexão para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="daf75-711">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="daf75-712">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-712">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="daf75-713">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="daf75-713">Transport configuration</span></span>

<span data-ttu-id="daf75-714">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="daf75-714">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="daf75-715">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="daf75-715">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="daf75-716">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="daf75-716">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="daf75-717">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="daf75-717">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="daf75-718">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="daf75-718">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="daf75-719">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="daf75-719">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="daf75-720">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="daf75-720">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="daf75-721">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="daf75-721">URL prefixes</span></span>

<span data-ttu-id="daf75-722">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="daf75-722">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="daf75-723">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="daf75-723">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="daf75-724">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-724">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="daf75-725">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-725">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="daf75-726">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="daf75-726">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="daf75-727">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-727">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="daf75-728">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="daf75-728">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="daf75-729">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-729">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="daf75-730">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="daf75-730">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="daf75-731">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="daf75-731">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="daf75-732">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="daf75-732">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="daf75-733">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="daf75-733">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="daf75-734">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-734">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="daf75-735">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="daf75-735">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="daf75-736">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="daf75-736">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="daf75-737">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="daf75-737">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="daf75-738">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="daf75-738">Host filtering</span></span>

<span data-ttu-id="daf75-739">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="daf75-739">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="daf75-740">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="daf75-740">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="daf75-741">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="daf75-741">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="daf75-742">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="daf75-742">`Host` headers aren't validated.</span></span>

<span data-ttu-id="daf75-743">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="daf75-743">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="daf75-744">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="daf75-744">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="daf75-745">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="daf75-745">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="daf75-746">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-746">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="daf75-747">Para habilitar o middleware, defina uma chave `AllowedHosts` em *appSettings. JSON*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="daf75-747">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="daf75-748">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="daf75-748">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="daf75-749">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="daf75-749">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="daf75-750">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="daf75-750">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="daf75-751">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="daf75-751">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="daf75-752">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="daf75-752">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="daf75-753">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="daf75-753">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="daf75-754">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="daf75-754">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="daf75-755">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-755">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="daf75-756">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="daf75-756">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="daf75-757">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="daf75-757">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="daf75-758">HTTPS</span><span class="sxs-lookup"><span data-stu-id="daf75-758">HTTPS</span></span>
* <span data-ttu-id="daf75-759">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="daf75-759">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="daf75-760">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="daf75-760">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="daf75-761">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="daf75-761">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="daf75-762">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daf75-762">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="daf75-763">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="daf75-763">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="daf75-764">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="daf75-764">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="daf75-765">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-765">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="daf75-766">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="daf75-766">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="daf75-768">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="daf75-768">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="daf75-770">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="daf75-770">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="daf75-771">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="daf75-771">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="daf75-772">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="daf75-772">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="daf75-773">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="daf75-773">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="daf75-774">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="daf75-774">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="daf75-775">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="daf75-775">A reverse proxy:</span></span>

* <span data-ttu-id="daf75-776">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="daf75-776">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="daf75-777">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="daf75-777">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="daf75-778">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="daf75-778">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="daf75-779">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="daf75-779">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="daf75-780">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="daf75-780">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="daf75-781">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="daf75-781">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="daf75-782">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daf75-782">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="daf75-783">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="daf75-783">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="daf75-784">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-784">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="daf75-785">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="daf75-785">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="daf75-786">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="daf75-786">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="daf75-787">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="daf75-787">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="daf75-788">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="daf75-788">Kestrel options</span></span>

<span data-ttu-id="daf75-789">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="daf75-789">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="daf75-790">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="daf75-790">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="daf75-791">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="daf75-791">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="daf75-792">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="daf75-792">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="daf75-793">As opções de Kestrel, que são C# configuradas no código nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-793">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="daf75-794">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="daf75-794">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="daf75-795">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="daf75-795">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="daf75-796">Configurar Kestrel no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="daf75-796">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="daf75-797">Injetar uma instância de `IConfiguration` na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="daf75-797">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="daf75-798">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à propriedade `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="daf75-798">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="daf75-799">No `Startup.ConfigureServices`, carregue a seção `Kestrel` da configuração na configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-799">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="daf75-800">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="daf75-800">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="daf75-801">No *Program.cs*, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="daf75-801">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="daf75-802">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="daf75-802">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="daf75-803">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="daf75-803">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="daf75-804">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="daf75-804">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="daf75-805">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="daf75-805">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="daf75-806">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="daf75-806">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="daf75-807">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-807">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="daf75-808">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="daf75-808">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="daf75-809">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="daf75-809">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="daf75-810">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-810">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="daf75-811">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-811">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="daf75-812">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="daf75-812">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="daf75-813">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="daf75-813">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="daf75-814">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="daf75-814">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="daf75-815">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="daf75-815">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="daf75-816">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-816">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="daf75-817">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="daf75-817">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="daf75-818">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="daf75-818">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="daf75-819">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-819">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="daf75-820">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="daf75-820">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="daf75-821">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="daf75-821">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="daf75-822">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="daf75-822">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="daf75-823">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="daf75-823">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="daf75-824">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="daf75-824">A minimum rate also applies to the response.</span></span> <span data-ttu-id="daf75-825">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="daf75-825">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="daf75-826">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="daf75-826">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="daf75-827">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="daf75-827">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="daf75-828">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="daf75-828">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="daf75-829">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="daf75-829">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="daf75-830">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="daf75-830">Synchronous IO</span></span>

<span data-ttu-id="daf75-831"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a E/S síncrona é permitida para a solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="daf75-831"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="daf75-832">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="daf75-832">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="daf75-833">Um grande número de operações de E/S síncronas de bloqueio pode levar à privação de pool de thread, o que faz com que o aplicativo não responda.</span><span class="sxs-lookup"><span data-stu-id="daf75-833">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="daf75-834">Habilite `AllowSynchronousIO` somente ao usar uma biblioteca em que não há suporte para E/S assíncrona.</span><span class="sxs-lookup"><span data-stu-id="daf75-834">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="daf75-835">O exemplo a seguir desativa a E/S síncrona:</span><span class="sxs-lookup"><span data-stu-id="daf75-835">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="daf75-836">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="daf75-836">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="daf75-837">Configuração do ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="daf75-837">Endpoint configuration</span></span>

<span data-ttu-id="daf75-838">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="daf75-838">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="daf75-839">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="daf75-839">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="daf75-840">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="daf75-840">Specify URLs using the:</span></span>

* <span data-ttu-id="daf75-841">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="daf75-841">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="daf75-842">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-842">`--urls` command-line argument.</span></span>
* <span data-ttu-id="daf75-843">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-843">`urls` host configuration key.</span></span>
* <span data-ttu-id="daf75-844">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-844">`UseUrls` extension method.</span></span>

<span data-ttu-id="daf75-845">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="daf75-845">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="daf75-846">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000; http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="daf75-846">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="daf75-847">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="daf75-847">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="daf75-848">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="daf75-848">A development certificate is created:</span></span>

* <span data-ttu-id="daf75-849">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="daf75-849">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="daf75-850">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-850">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="daf75-851">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="daf75-851">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="daf75-852">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="daf75-852">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="daf75-853">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-853">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="daf75-854">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="daf75-854">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="daf75-855">configuração do `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="daf75-855">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="daf75-856">ConfigureEndpointDefaults (ação\<ListenerOptions >)</span><span class="sxs-lookup"><span data-stu-id="daf75-856">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="daf75-857">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-857">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="daf75-858">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="daf75-858">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="daf75-859">ConfigureHttpsDefaults (ação\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="daf75-859">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="daf75-860">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="daf75-860">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="daf75-861">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="daf75-861">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a><span data-ttu-id="daf75-862">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="daf75-862">Configure(IConfiguration)</span></span>

<span data-ttu-id="daf75-863">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="daf75-863">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="daf75-864">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-864">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="daf75-865">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="daf75-865">ListenOptions.UseHttps</span></span>

<span data-ttu-id="daf75-866">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="daf75-866">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="daf75-867">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="daf75-867">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="daf75-868">`UseHttps` &ndash; Configure o Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-868">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="daf75-869">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="daf75-869">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="daf75-870">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="daf75-870">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="daf75-871">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="daf75-871">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="daf75-872">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="daf75-872">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="daf75-873">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="daf75-873">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="daf75-874">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="daf75-874">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="daf75-875">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="daf75-875">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="daf75-876">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="daf75-876">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="daf75-877">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="daf75-877">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="daf75-878">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="daf75-878">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="daf75-879">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="daf75-879">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="daf75-880">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="daf75-880">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="daf75-881">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="daf75-881">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="daf75-882">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-882">Supported configurations described next:</span></span>

* <span data-ttu-id="daf75-883">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="daf75-883">No configuration</span></span>
* <span data-ttu-id="daf75-884">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="daf75-884">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="daf75-885">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="daf75-885">Change the defaults in code</span></span>

<span data-ttu-id="daf75-886">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-886">*No configuration*</span></span>

<span data-ttu-id="daf75-887">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="daf75-887">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="daf75-888">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="daf75-888">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="daf75-889">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-889">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="daf75-890">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-890">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="daf75-891">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="daf75-891">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="daf75-892">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="daf75-892">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="daf75-893">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="daf75-893">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="daf75-894">Todo ponto de extremidade HTTPS que não especificar um certificado (**HttpsDefaultCert** no exemplo a seguir) será revertido para o certificado definido em **Certificados** > **Padrão** ou para o certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="daf75-894">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
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
          "Store": "<certificate store; required>",
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

<span data-ttu-id="daf75-895">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="daf75-895">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="daf75-896">Por exemplo, o certificado **Certificados** > **Padrão** pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="daf75-896">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="daf75-897">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="daf75-897">Schema notes:</span></span>

* <span data-ttu-id="daf75-898">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="daf75-898">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="daf75-899">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="daf75-899">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="daf75-900">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="daf75-900">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="daf75-901">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="daf75-901">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="daf75-902">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="daf75-902">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="daf75-903">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-903">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="daf75-904">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="daf75-904">The `Certificate` section is optional.</span></span> <span data-ttu-id="daf75-905">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="daf75-905">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="daf75-906">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="daf75-906">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="daf75-907">A seção `Certificate` é compatível com os certificados de **Caminho**&ndash;**Senha** e **Entidade**&ndash;**Repositório**.</span><span class="sxs-lookup"><span data-stu-id="daf75-907">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="daf75-908">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="daf75-908">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="daf75-909">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="daf75-909">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="daf75-910">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido por <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="daf75-910">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="daf75-911">A seção de configuração para cada ponto de extremidade está disponível nas opções no método `Endpoint` para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="daf75-911">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="daf75-912">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="daf75-912">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="daf75-913">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="daf75-913">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="daf75-914">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="daf75-914">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="daf75-915">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="daf75-915">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="daf75-916">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="daf75-916">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="daf75-917">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="daf75-917">*Change the defaults in code*</span></span>

<span data-ttu-id="daf75-918">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="daf75-918">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="daf75-919">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="daf75-919">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="daf75-920">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="daf75-920">*Kestrel support for SNI*</span></span>

<span data-ttu-id="daf75-921">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="daf75-921">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="daf75-922">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="daf75-922">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="daf75-923">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-923">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="daf75-924">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="daf75-924">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="daf75-925">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="daf75-925">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="daf75-926">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="daf75-926">SNI support requires:</span></span>

* <span data-ttu-id="daf75-927">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="daf75-927">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="daf75-928">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` é sempre `null`.</span><span class="sxs-lookup"><span data-stu-id="daf75-928">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="daf75-929">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="daf75-929">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="daf75-930">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-930">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="daf75-931">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="daf75-931">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
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

### <a name="connection-logging"></a><span data-ttu-id="daf75-932">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="daf75-932">Connection logging</span></span>

<span data-ttu-id="daf75-933">Chame <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="daf75-933">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="daf75-934">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="daf75-934">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="daf75-935">Se `UseConnectionLogging` for colocado antes `UseHttps`, o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="daf75-935">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="daf75-936">Se `UseConnectionLogging` for colocado após `UseHttps`, o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="daf75-936">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="daf75-937">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="daf75-937">Bind to a TCP socket</span></span>

<span data-ttu-id="daf75-938">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="daf75-938">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
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
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="daf75-939">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="daf75-939">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="daf75-940">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="daf75-940">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="daf75-941">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="daf75-941">Bind to a Unix socket</span></span>

<span data-ttu-id="daf75-942">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="daf75-942">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

### <a name="port-0"></a><span data-ttu-id="daf75-943">Porta 0</span><span class="sxs-lookup"><span data-stu-id="daf75-943">Port 0</span></span>

<span data-ttu-id="daf75-944">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="daf75-944">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="daf75-945">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="daf75-945">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="daf75-946">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="daf75-946">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="daf75-947">Limitações</span><span class="sxs-lookup"><span data-stu-id="daf75-947">Limitations</span></span>

<span data-ttu-id="daf75-948">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="daf75-948">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="daf75-949">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="daf75-949">`--urls` command-line argument</span></span>
* <span data-ttu-id="daf75-950">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="daf75-950">`urls` host configuration key</span></span>
* <span data-ttu-id="daf75-951">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="daf75-951">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="daf75-952">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="daf75-952">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="daf75-953">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="daf75-953">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="daf75-954">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="daf75-954">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="daf75-955">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-955">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="daf75-956">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="daf75-956">IIS endpoint configuration</span></span>

<span data-ttu-id="daf75-957">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-957">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="daf75-958">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="daf75-958">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="daf75-959">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="daf75-959">Transport configuration</span></span>

<span data-ttu-id="daf75-960">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="daf75-960">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="daf75-961">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="daf75-961">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="daf75-962">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="daf75-962">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="daf75-963">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="daf75-963">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="daf75-964">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="daf75-964">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="daf75-965">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="daf75-965">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="daf75-966">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="daf75-966">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="daf75-967">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="daf75-967">URL prefixes</span></span>

<span data-ttu-id="daf75-968">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="daf75-968">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="daf75-969">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="daf75-969">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="daf75-970">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="daf75-970">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="daf75-971">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-971">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="daf75-972">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="daf75-972">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="daf75-973">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-973">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="daf75-974">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="daf75-974">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="daf75-975">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-975">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="daf75-976">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="daf75-976">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="daf75-977">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="daf75-977">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="daf75-978">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="daf75-978">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="daf75-979">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="daf75-979">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="daf75-980">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="daf75-980">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="daf75-981">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="daf75-981">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="daf75-982">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="daf75-982">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="daf75-983">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="daf75-983">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="daf75-984">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="daf75-984">Host filtering</span></span>

<span data-ttu-id="daf75-985">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="daf75-985">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="daf75-986">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="daf75-986">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="daf75-987">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="daf75-987">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="daf75-988">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="daf75-988">`Host` headers aren't validated.</span></span>

<span data-ttu-id="daf75-989">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="daf75-989">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="daf75-990">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="daf75-990">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="daf75-991">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="daf75-991">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="daf75-992">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="daf75-992">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="daf75-993">Para habilitar o middleware, defina uma chave `AllowedHosts` em *appSettings. JSON*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="daf75-993">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="daf75-994">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="daf75-994">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="daf75-995">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="daf75-995">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="daf75-996">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="daf75-996">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="daf75-997">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="daf75-997">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="daf75-998">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="daf75-998">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="daf75-999">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="daf75-999">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="daf75-1000">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="daf75-1000">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="daf75-1001">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="daf75-1001">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="daf75-1002">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="daf75-1002">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
