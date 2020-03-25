---
title: Implementação do servidor Web Kestrel no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o Kestrel, o servidor Web multiplataforma do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: e9b4b57ee70e4050f9399b90a6e34e8cc9cca78d
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218824"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="ac2f2-103">Implementação do servidor Web Kestrel no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac2f2-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="ac2f2-104">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ac2f2-105">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ac2f2-106">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ac2f2-107">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="ac2f2-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ac2f2-108">HTTPS</span></span>
* <span data-ttu-id="ac2f2-109">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="ac2f2-110">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="ac2f2-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="ac2f2-111">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="ac2f2-112">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="ac2f2-113">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ac2f2-114">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac2f2-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="ac2f2-115">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ac2f2-115">HTTP/2 support</span></span>

<span data-ttu-id="ac2f2-116">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ac2f2-117">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="ac2f2-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="ac2f2-118">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ac2f2-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="ac2f2-119">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="ac2f2-120">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ac2f2-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="ac2f2-121">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ac2f2-122">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ac2f2-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ac2f2-123">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="ac2f2-124">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="ac2f2-125">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="ac2f2-126">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="ac2f2-127">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="ac2f2-128">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="ac2f2-129">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ac2f2-130">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="ac2f2-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="ac2f2-131">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ac2f2-132">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="ac2f2-133">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="ac2f2-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ac2f2-135">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-135">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ac2f2-137">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="ac2f2-138">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="ac2f2-139">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="ac2f2-140">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ac2f2-141">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="ac2f2-142">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-142">A reverse proxy:</span></span>

* <span data-ttu-id="ac2f2-143">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ac2f2-144">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="ac2f2-145">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="ac2f2-146">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="ac2f2-147">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="ac2f2-148">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ac2f2-149">KESTREL em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac2f2-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="ac2f2-150">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="ac2f2-151">No *Program.cs*, o método <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="ac2f2-152">Para obter mais informações sobre como criar o host, consulte as seções *configurar um host* e *as configurações do construtor padrão* de <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="ac2f2-153">Para fornecer configuração adicional após chamar `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="ac2f2-154">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="ac2f2-154">Kestrel options</span></span>

<span data-ttu-id="ac2f2-155">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="ac2f2-156">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="ac2f2-157">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="ac2f2-158">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="ac2f2-159">Nos exemplos mostrados posteriormente neste artigo, as opções de Kestrel são C# configuradas no código.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="ac2f2-160">As opções de Kestrel também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="ac2f2-161">Por exemplo, o [provedor de configuração de arquivo](xref:fundamentals/configuration/index#file-configuration-provider) pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ac2f2-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

> [!NOTE]
> <span data-ttu-id="ac2f2-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> e a [configuração de ponto de extremidade](#endpoint-configuration) são configuráveis a partir de provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="ac2f2-163">A configuração restante do Kestrel deve ser C# configurada no código.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="ac2f2-164">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="ac2f2-165">Configurar Kestrel no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="ac2f2-166">Injetar uma instância do `IConfiguration` na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="ac2f2-167">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à propriedade `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="ac2f2-168">Em `Startup.ConfigureServices`, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="ac2f2-169">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="ac2f2-170">No *Program.cs*, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="ac2f2-171">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="ac2f2-172">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="ac2f2-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ac2f2-173">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ac2f2-174">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="ac2f2-175">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="ac2f2-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ac2f2-176">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="ac2f2-177">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="ac2f2-178">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="ac2f2-179">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ac2f2-180">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ac2f2-181">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="ac2f2-182">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ac2f2-183">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="ac2f2-184">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ac2f2-185">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ac2f2-186">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ac2f2-187">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ac2f2-188">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ac2f2-189">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ac2f2-190">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ac2f2-191">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="ac2f2-192">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ac2f2-193">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ac2f2-194">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="ac2f2-195">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="ac2f2-196">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="ac2f2-197">O <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenciado no exemplo anterior não está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é geralmente compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="ac2f2-198">No entanto, o <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> ainda está presente em `HttpContext.Features` para solicitações HTTP/2, pois o limite de taxa de leitura ainda pode ser *desabilitado totalmente* em cada solicitação, definindo `IHttpMinRequestBodyDataRateFeature.MinDataRate` para `null` mesmo em uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="ac2f2-199">Tentar ler `IHttpMinRequestBodyDataRateFeature.MinDataRate` ou tentar defini-lo como um valor diferente de `null` resultará na geração de um `NotSupportedException` devido a uma solicitação HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="ac2f2-200">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="ac2f2-201">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ac2f2-202">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="ac2f2-203">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="ac2f2-204">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="ac2f2-204">Maximum streams per connection</span></span>

<span data-ttu-id="ac2f2-205">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="ac2f2-206">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="ac2f2-207">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="ac2f2-208">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="ac2f2-208">Header table size</span></span>

<span data-ttu-id="ac2f2-209">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="ac2f2-210">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="ac2f2-211">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="ac2f2-212">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="ac2f2-213">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="ac2f2-213">Maximum frame size</span></span>

<span data-ttu-id="ac2f2-214">`Http2.MaxFrameSize` indica o tamanho máximo permitido de um conteúdo de quadro de conexão HTTP/2 recebido ou enviado pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="ac2f2-215">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="ac2f2-216">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="ac2f2-217">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-217">Maximum request header size</span></span>

<span data-ttu-id="ac2f2-218">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="ac2f2-219">Esse limite se aplica tanto ao nome quanto ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="ac2f2-220">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="ac2f2-221">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="ac2f2-222">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="ac2f2-222">Initial connection window size</span></span>

<span data-ttu-id="ac2f2-223">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="ac2f2-224">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ac2f2-225">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="ac2f2-226">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="ac2f2-227">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="ac2f2-227">Initial stream window size</span></span>

<span data-ttu-id="ac2f2-228">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="ac2f2-229">As solicitações também são limitadas por `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="ac2f2-230">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="ac2f2-231">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="ac2f2-232">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="ac2f2-232">Synchronous IO</span></span>

<span data-ttu-id="ac2f2-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a E/S síncrona é permitida para a solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="ac2f2-234">O valor padrão é `false`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="ac2f2-235">Um grande número de operações de E/S síncronas de bloqueio pode levar à privação de pool de thread, o que faz com que o aplicativo não responda.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-235">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="ac2f2-236">Habilite `AllowSynchronousIO` somente ao usar uma biblioteca em que não há suporte para E/S assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="ac2f2-237">O exemplo a seguir ativa a E/S síncrona:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-237">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="ac2f2-238">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ac2f2-239">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="ac2f2-239">Endpoint configuration</span></span>

<span data-ttu-id="ac2f2-240">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ac2f2-241">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="ac2f2-242">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-242">Specify URLs using the:</span></span>

* <span data-ttu-id="ac2f2-243">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="ac2f2-244">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="ac2f2-245">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="ac2f2-246">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="ac2f2-247">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="ac2f2-248">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ac2f2-249">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ac2f2-250">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-250">A development certificate is created:</span></span>

* <span data-ttu-id="ac2f2-251">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="ac2f2-252">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="ac2f2-253">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="ac2f2-254">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ac2f2-255">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="ac2f2-256">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="ac2f2-257">configuração de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ac2f2-258">ConfigureEndpointDefaults (ação\<ListenerOptions >)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="ac2f2-259">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ac2f2-260">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="ac2f2-261">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de chamar <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ac2f2-262">ConfigureHttpsDefaults (ação\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ac2f2-263">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ac2f2-264">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="ac2f2-265">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de chamar <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="ac2f2-266">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="ac2f2-267">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="ac2f2-268">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ac2f2-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ac2f2-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="ac2f2-270">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="ac2f2-271">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="ac2f2-272">`UseHttps` &ndash; configurar o Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="ac2f2-273">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="ac2f2-274">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="ac2f2-275">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ac2f2-276">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="ac2f2-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ac2f2-277">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="ac2f2-278">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ac2f2-279">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="ac2f2-280">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="ac2f2-281">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="ac2f2-282">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="ac2f2-283">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="ac2f2-284">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ac2f2-285">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ac2f2-286">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-286">Supported configurations described next:</span></span>

* <span data-ttu-id="ac2f2-287">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="ac2f2-287">No configuration</span></span>
* <span data-ttu-id="ac2f2-288">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="ac2f2-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="ac2f2-289">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="ac2f2-289">Change the defaults in code</span></span>

<span data-ttu-id="ac2f2-290">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-290">*No configuration*</span></span>

<span data-ttu-id="ac2f2-291">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="ac2f2-292">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="ac2f2-293">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="ac2f2-294">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="ac2f2-295">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="ac2f2-296">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="ac2f2-297">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="ac2f2-298">Qualquer ponto de extremidade HTTPS que não especifique um certificado (**HttpsDefaultCert** no exemplo a seguir) retornará ao certificado definido em **certificados** > **padrão** ou ao certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="ac2f2-299">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="ac2f2-300">Por exemplo, o certificado **Certificados** > **Padrão** pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ac2f2-301">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-301">Schema notes:</span></span>

* <span data-ttu-id="ac2f2-302">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ac2f2-303">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ac2f2-304">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ac2f2-305">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="ac2f2-306">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="ac2f2-307">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ac2f2-308">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="ac2f2-309">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="ac2f2-310">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="ac2f2-311">A seção `Certificate` é compatível com os certificados de **Caminho**&ndash;**Senha** e **Entidade**&ndash;**Repositório**.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ac2f2-312">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="ac2f2-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="ac2f2-314">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="ac2f2-315">A seção de configuração para cada ponto de extremidade está disponível nas opções no método `Endpoint` para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="ac2f2-316">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="ac2f2-317">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="ac2f2-318">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ac2f2-319">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ac2f2-320">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ac2f2-321">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-321">*Change the defaults in code*</span></span>

<span data-ttu-id="ac2f2-322">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ac2f2-323">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="ac2f2-324">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="ac2f2-325">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="ac2f2-326">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="ac2f2-327">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="ac2f2-328">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ac2f2-329">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ac2f2-330">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-330">SNI support requires:</span></span>

* <span data-ttu-id="ac2f2-331">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="ac2f2-332">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` é sempre `null`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="ac2f2-333">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="ac2f2-334">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="ac2f2-335">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="ac2f2-336">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="ac2f2-336">Connection logging</span></span>

<span data-ttu-id="ac2f2-337">Chame <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="ac2f2-338">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="ac2f2-339">Se `UseConnectionLogging` for colocado antes `UseHttps`, o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="ac2f2-340">Se `UseConnectionLogging` for colocado após `UseHttps`, o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ac2f2-341">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="ac2f2-341">Bind to a TCP socket</span></span>

<span data-ttu-id="ac2f2-342">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="ac2f2-343">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="ac2f2-344">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ac2f2-345">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="ac2f2-345">Bind to a Unix socket</span></span>

<span data-ttu-id="ac2f2-346">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="ac2f2-347">No arquivo de configuração Nginx, defina a `server` > `location` > entrada `proxy_pass` para `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="ac2f2-348">`{KESTREL SOCKET}` é o nome do soquete fornecido a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="ac2f2-349">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="ac2f2-350">Porta 0</span><span class="sxs-lookup"><span data-stu-id="ac2f2-350">Port 0</span></span>

<span data-ttu-id="ac2f2-351">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ac2f2-352">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ac2f2-353">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ac2f2-354">Limitações</span><span class="sxs-lookup"><span data-stu-id="ac2f2-354">Limitations</span></span>

<span data-ttu-id="ac2f2-355">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ac2f2-356">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="ac2f2-357">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-357">`urls` host configuration key</span></span>
* <span data-ttu-id="ac2f2-358">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ac2f2-359">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="ac2f2-360">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="ac2f2-361">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="ac2f2-362">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ac2f2-363">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="ac2f2-363">IIS endpoint configuration</span></span>

<span data-ttu-id="ac2f2-364">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ac2f2-365">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="ac2f2-366">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="ac2f2-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="ac2f2-367">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="ac2f2-368">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="ac2f2-369">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="ac2f2-370">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="ac2f2-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="ac2f2-371">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-371">HTTP/1.1 only.</span></span> <span data-ttu-id="ac2f2-372">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="ac2f2-373">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-373">HTTP/2 only.</span></span> <span data-ttu-id="ac2f2-374">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="ac2f2-375">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="ac2f2-376">O HTTP/2 requer que o cliente selecione HTTP/2 no handshake de [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="ac2f2-377">O valor de `ListenOptions.Protocols` padrão para qualquer ponto de extremidade é `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="ac2f2-378">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="ac2f2-379">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ac2f2-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="ac2f2-380">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="ac2f2-380">Renegotiation disabled</span></span>
* <span data-ttu-id="ac2f2-381">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="ac2f2-381">Compression disabled</span></span>
* <span data-ttu-id="ac2f2-382">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="ac2f2-383">Diffie-Hellman de curva elíptica (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits mínimo</span><span class="sxs-lookup"><span data-stu-id="ac2f2-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="ac2f2-384">Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="ac2f2-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="ac2f2-385">Pacote de criptografia não autorizado</span><span class="sxs-lookup"><span data-stu-id="ac2f2-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="ac2f2-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 &lbrack;`FIPS186`&rbrack; tem suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="ac2f2-387">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="ac2f2-388">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="ac2f2-389">Use o middleware de conexão para filtrar os Handshakes TLS em uma base por conexão para codificações específicas, se necessário.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="ac2f2-390">O exemplo a seguir gera <xref:System.NotSupportedException> para qualquer algoritmo de codificação para o qual o aplicativo não dá suporte.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="ac2f2-391">Como alternativa, defina e compare [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) com uma lista de pacotes de codificação aceitáveis.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="ac2f2-392">Nenhuma criptografia é usada com um algoritmo de codificação [CipherAlgorithmType. NULL](xref:System.Security.Authentication.CipherAlgorithmType) .</span><span class="sxs-lookup"><span data-stu-id="ac2f2-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="ac2f2-393">A filtragem de conexão também pode ser configurada por meio de um <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="ac2f2-394">No Linux, <xref:System.Net.Security.CipherSuitesPolicy> pode ser usado para filtrar os Handshakes de TLS por conexão:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="ac2f2-395">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="ac2f2-396">`CreateDefaultBuilder` chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="ac2f2-397">O exemplo *appSettings. JSON* a seguir estabelece o http/1.1 como o protocolo de conexão padrão para todos os pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="ac2f2-398">O exemplo *appSettings. JSON* a seguir estabelece o protocolo de conexão HTTP/1.1 para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="ac2f2-399">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ac2f2-400">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="ac2f2-400">Transport configuration</span></span>

<span data-ttu-id="ac2f2-401">Para projetos que exigem o uso de Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="ac2f2-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="ac2f2-402">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="ac2f2-403">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> no `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="ac2f2-404">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="ac2f2-404">URL prefixes</span></span>

<span data-ttu-id="ac2f2-405">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="ac2f2-406">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ac2f2-407">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="ac2f2-408">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ac2f2-409">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ac2f2-410">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ac2f2-411">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ac2f2-412">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ac2f2-413">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ac2f2-414">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ac2f2-415">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ac2f2-416">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="ac2f2-417">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ac2f2-418">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ac2f2-419">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ac2f2-420">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ac2f2-421">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="ac2f2-421">Host filtering</span></span>

<span data-ttu-id="ac2f2-422">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="ac2f2-423">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ac2f2-424">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ac2f2-425">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="ac2f2-426">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="ac2f2-427">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que é fornecido implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="ac2f2-428">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ac2f2-429">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="ac2f2-430">Para habilitar o middleware, defina uma chave `AllowedHosts` em *appSettings. JSON*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="ac2f2-431">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="ac2f2-432">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ac2f2-433">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="ac2f2-434">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="ac2f2-435">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="ac2f2-436">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="ac2f2-437">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ac2f2-438">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ac2f2-439">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ac2f2-440">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="ac2f2-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ac2f2-441">HTTPS</span></span>
* <span data-ttu-id="ac2f2-442">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="ac2f2-443">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="ac2f2-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="ac2f2-444">HTTP/2 (exceto em macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="ac2f2-445">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="ac2f2-446">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ac2f2-447">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac2f2-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="ac2f2-448">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ac2f2-448">HTTP/2 support</span></span>

<span data-ttu-id="ac2f2-449">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará disponível para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ac2f2-450">Sistema operacional&dagger;</span><span class="sxs-lookup"><span data-stu-id="ac2f2-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="ac2f2-451">Windows Server 2016/Windows 10 ou posterior&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ac2f2-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="ac2f2-452">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="ac2f2-453">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ac2f2-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="ac2f2-454">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ac2f2-455">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ac2f2-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ac2f2-456">&dagger;O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="ac2f2-457">&Dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="ac2f2-458">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="ac2f2-459">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="ac2f2-460">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="ac2f2-461">HTTP/2 está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="ac2f2-462">Para obter mais informações sobre a configuração, consulte as seções [Opções do Kestrel](#kestrel-options) e [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ac2f2-463">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="ac2f2-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="ac2f2-464">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ac2f2-465">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="ac2f2-466">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="ac2f2-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ac2f2-468">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-468">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ac2f2-470">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="ac2f2-471">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="ac2f2-472">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="ac2f2-473">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ac2f2-474">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="ac2f2-475">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-475">A reverse proxy:</span></span>

* <span data-ttu-id="ac2f2-476">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ac2f2-477">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="ac2f2-478">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="ac2f2-479">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="ac2f2-480">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="ac2f2-481">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ac2f2-482">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac2f2-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="ac2f2-483">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ac2f2-484">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="ac2f2-485">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="ac2f2-486">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="ac2f2-487">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, use `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="ac2f2-488">Se o aplicativo não chamar `CreateDefaultBuilder` para configurar o host, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **antes** de chamar `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="ac2f2-489">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="ac2f2-489">Kestrel options</span></span>

<span data-ttu-id="ac2f2-490">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="ac2f2-491">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="ac2f2-492">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="ac2f2-493">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="ac2f2-494">As opções de Kestrel, que são C# configuradas no código nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="ac2f2-495">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ac2f2-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="ac2f2-496">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="ac2f2-497">Configurar Kestrel no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="ac2f2-498">Injetar uma instância do `IConfiguration` na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="ac2f2-499">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à propriedade `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="ac2f2-500">Em `Startup.ConfigureServices`, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="ac2f2-501">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="ac2f2-502">No *Program.cs*, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="ac2f2-503">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="ac2f2-504">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="ac2f2-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ac2f2-505">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ac2f2-506">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="ac2f2-507">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="ac2f2-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ac2f2-508">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="ac2f2-509">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="ac2f2-510">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="ac2f2-511">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ac2f2-512">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ac2f2-513">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="ac2f2-514">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ac2f2-515">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="ac2f2-516">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ac2f2-517">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ac2f2-518">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ac2f2-519">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ac2f2-520">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ac2f2-521">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ac2f2-522">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ac2f2-523">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="ac2f2-524">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ac2f2-525">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ac2f2-526">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="ac2f2-527">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="ac2f2-528">Substitua os limites de taxa mínimo por solicitação no middleware:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="ac2f2-529">Nenhum desses recursos de taxa referenciados no exemplo anterior está presente no `HttpContext.Features` para solicitações HTTP/2, porque a modificação dos limites de taxa em cada solicitação não é compatível com HTTP/2 devido ao suporte de multiplexação de solicitação do protocolo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="ac2f2-530">Os limites de taxa de todo o servidor configurados por meio de `KestrelServerOptions.Limits` ainda se aplicam a conexões HTTP/1.x e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="ac2f2-531">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ac2f2-532">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="ac2f2-533">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="ac2f2-534">Fluxos máximos por conexão</span><span class="sxs-lookup"><span data-stu-id="ac2f2-534">Maximum streams per connection</span></span>

<span data-ttu-id="ac2f2-535">O `Http2.MaxStreamsPerConnection` limita o número de fluxos de solicitações simultâneas por conexão HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="ac2f2-536">Os fluxos em excesso são recusados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="ac2f2-537">O valor padrão é 100.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="ac2f2-538">Tamanho da tabela de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="ac2f2-538">Header table size</span></span>

<span data-ttu-id="ac2f2-539">O decodificador HPACK descompacta os cabeçalhos HTTP para conexões HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="ac2f2-540">O `Http2.HeaderTableSize` limita o tamanho da tabela de compactação de cabeçalho usada pelo decodificador HPACK.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="ac2f2-541">O valor é fornecido em octetos e deve ser maior do que zero (0).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="ac2f2-542">O valor padrão é 4096.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="ac2f2-543">Tamanho máximo do quadro</span><span class="sxs-lookup"><span data-stu-id="ac2f2-543">Maximum frame size</span></span>

<span data-ttu-id="ac2f2-544">O `Http2.MaxFrameSize` indica o tamanho máximo do payload do quadro da conexão HTTP/2 a ser recebido.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="ac2f2-545">O valor é fornecido em octetos e deve estar entre 2^14 (16.384) e 2^24-1 (16.777.215).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="ac2f2-546">O valor padrão é 2^14 (16.384).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="ac2f2-547">Tamanho máximo do cabeçalho de solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-547">Maximum request header size</span></span>

<span data-ttu-id="ac2f2-548">`Http2.MaxRequestHeaderFieldSize` indica o tamanho máximo permitido em octetos de valores de cabeçalho de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="ac2f2-549">Esse limite se aplica ao nome e ao valor em suas representações compactadas e descompactadas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="ac2f2-550">O valor deve ser maior que zero (0).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="ac2f2-551">O valor padrão é 8.192.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="ac2f2-552">Tamanho inicial da janela de conexão</span><span class="sxs-lookup"><span data-stu-id="ac2f2-552">Initial connection window size</span></span>

<span data-ttu-id="ac2f2-553">`Http2.InitialConnectionWindowSize` indica os dados máximos do corpo da solicitação em bytes, que o servidor armazena em buffer ao mesmo tempo, agregados em todas as solicitações (fluxos) por conexão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="ac2f2-554">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ac2f2-555">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="ac2f2-556">O valor padrão é 128 KB (131.072).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="ac2f2-557">Tamanho inicial da janela de fluxo</span><span class="sxs-lookup"><span data-stu-id="ac2f2-557">Initial stream window size</span></span>

<span data-ttu-id="ac2f2-558">`Http2.InitialStreamWindowSize` indica o máximo de dados do corpo da solicitação, em bytes, que o servidor armazena em buffer ao mesmo tempo, por solicitação (fluxo).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="ac2f2-559">As solicitações também são limitadas por `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ac2f2-560">O valor deve ser maior ou igual a 65.535 e menor que 2^31 (2.147.483.648).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="ac2f2-561">O valor padrão é 96 KB (98.304).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="ac2f2-562">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="ac2f2-562">Synchronous IO</span></span>

<span data-ttu-id="ac2f2-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a E/S síncrona é permitida para a solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="ac2f2-564">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="ac2f2-565">Um grande número de operações de E/S síncronas de bloqueio pode levar à privação de pool de thread, o que faz com que o aplicativo não responda.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-565">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="ac2f2-566">Habilite `AllowSynchronousIO` somente ao usar uma biblioteca em que não há suporte para E/S assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="ac2f2-567">O exemplo a seguir ativa a E/S síncrona:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-567">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="ac2f2-568">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ac2f2-569">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="ac2f2-569">Endpoint configuration</span></span>

<span data-ttu-id="ac2f2-570">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ac2f2-571">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="ac2f2-572">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-572">Specify URLs using the:</span></span>

* <span data-ttu-id="ac2f2-573">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="ac2f2-574">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="ac2f2-575">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="ac2f2-576">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="ac2f2-577">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="ac2f2-578">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ac2f2-579">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ac2f2-580">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-580">A development certificate is created:</span></span>

* <span data-ttu-id="ac2f2-581">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="ac2f2-582">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="ac2f2-583">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="ac2f2-584">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ac2f2-585">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="ac2f2-586">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="ac2f2-587">configuração de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ac2f2-588">ConfigureEndpointDefaults (ação\<ListenerOptions >)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="ac2f2-589">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ac2f2-590">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="ac2f2-591">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de chamar <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ac2f2-592">ConfigureHttpsDefaults (ação\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ac2f2-593">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ac2f2-594">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="ac2f2-595">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de chamar <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="ac2f2-596">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="ac2f2-597">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="ac2f2-598">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ac2f2-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ac2f2-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="ac2f2-600">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="ac2f2-601">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="ac2f2-602">`UseHttps` &ndash; configurar o Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="ac2f2-603">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="ac2f2-604">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="ac2f2-605">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ac2f2-606">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="ac2f2-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ac2f2-607">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="ac2f2-608">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ac2f2-609">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="ac2f2-610">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="ac2f2-611">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="ac2f2-612">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="ac2f2-613">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="ac2f2-614">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ac2f2-615">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ac2f2-616">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-616">Supported configurations described next:</span></span>

* <span data-ttu-id="ac2f2-617">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="ac2f2-617">No configuration</span></span>
* <span data-ttu-id="ac2f2-618">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="ac2f2-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="ac2f2-619">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="ac2f2-619">Change the defaults in code</span></span>

<span data-ttu-id="ac2f2-620">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-620">*No configuration*</span></span>

<span data-ttu-id="ac2f2-621">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="ac2f2-622">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="ac2f2-623">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="ac2f2-624">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="ac2f2-625">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="ac2f2-626">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="ac2f2-627">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="ac2f2-628">Qualquer ponto de extremidade HTTPS que não especifique um certificado (**HttpsDefaultCert** no exemplo a seguir) retornará ao certificado definido em **certificados** > **padrão** ou ao certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="ac2f2-629">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="ac2f2-630">Por exemplo, o certificado **Certificados** > **Padrão** pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ac2f2-631">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-631">Schema notes:</span></span>

* <span data-ttu-id="ac2f2-632">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ac2f2-633">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ac2f2-634">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ac2f2-635">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="ac2f2-636">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="ac2f2-637">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ac2f2-638">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="ac2f2-639">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="ac2f2-640">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="ac2f2-641">A seção `Certificate` é compatível com os certificados de **Caminho**&ndash;**Senha** e **Entidade**&ndash;**Repositório**.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ac2f2-642">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="ac2f2-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="ac2f2-644">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="ac2f2-645">A seção de configuração para cada ponto de extremidade está disponível nas opções no método `Endpoint` para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="ac2f2-646">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="ac2f2-647">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="ac2f2-648">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ac2f2-649">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ac2f2-650">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ac2f2-651">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-651">*Change the defaults in code*</span></span>

<span data-ttu-id="ac2f2-652">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ac2f2-653">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="ac2f2-654">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="ac2f2-655">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="ac2f2-656">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="ac2f2-657">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="ac2f2-658">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ac2f2-659">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ac2f2-660">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-660">SNI support requires:</span></span>

* <span data-ttu-id="ac2f2-661">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="ac2f2-662">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` é sempre `null`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="ac2f2-663">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="ac2f2-664">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="ac2f2-665">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="ac2f2-666">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="ac2f2-666">Connection logging</span></span>

<span data-ttu-id="ac2f2-667">Chame <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="ac2f2-668">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="ac2f2-669">Se `UseConnectionLogging` for colocado antes `UseHttps`, o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="ac2f2-670">Se `UseConnectionLogging` for colocado após `UseHttps`, o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ac2f2-671">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="ac2f2-671">Bind to a TCP socket</span></span>

<span data-ttu-id="ac2f2-672">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="ac2f2-673">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="ac2f2-674">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ac2f2-675">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="ac2f2-675">Bind to a Unix socket</span></span>

<span data-ttu-id="ac2f2-676">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="ac2f2-677">No arquivo Nginx confiuguration, defina o `server` > `location` > entrada de `proxy_pass` para `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="ac2f2-678">`{KESTREL SOCKET}` é o nome do soquete fornecido a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="ac2f2-679">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="ac2f2-680">Porta 0</span><span class="sxs-lookup"><span data-stu-id="ac2f2-680">Port 0</span></span>

<span data-ttu-id="ac2f2-681">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ac2f2-682">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ac2f2-683">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ac2f2-684">Limitações</span><span class="sxs-lookup"><span data-stu-id="ac2f2-684">Limitations</span></span>

<span data-ttu-id="ac2f2-685">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ac2f2-686">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="ac2f2-687">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-687">`urls` host configuration key</span></span>
* <span data-ttu-id="ac2f2-688">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ac2f2-689">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="ac2f2-690">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="ac2f2-691">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="ac2f2-692">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ac2f2-693">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="ac2f2-693">IIS endpoint configuration</span></span>

<span data-ttu-id="ac2f2-694">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ac2f2-695">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="ac2f2-696">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="ac2f2-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="ac2f2-697">A propriedade `Protocols` estabelece os protocolos HTTP (`HttpProtocols`) habilitados em um ponto de extremidade de conexão ou para o servidor.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="ac2f2-698">Atribua um valor à propriedade `Protocols` com base na enumeração `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="ac2f2-699">Valor de enumeração `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="ac2f2-700">Protocolo de conexão permitido</span><span class="sxs-lookup"><span data-stu-id="ac2f2-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="ac2f2-701">HTTP/1.1 apenas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-701">HTTP/1.1 only.</span></span> <span data-ttu-id="ac2f2-702">Pode ser usado com ou sem TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="ac2f2-703">HTTP/2 apenas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-703">HTTP/2 only.</span></span> <span data-ttu-id="ac2f2-704">Poderá ser usado sem TLS apenas se o cliente for compatível com um [Modo de conhecimento prévio](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="ac2f2-705">HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="ac2f2-706">O HTTP/2 requer uma conexão TLS e [ALPN (negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) ; caso contrário, a conexão será padronizada como HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="ac2f2-707">O protocolo padrão é HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="ac2f2-708">Restrições TLS para HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="ac2f2-709">Versão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ac2f2-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="ac2f2-710">Renegociação desabilitada</span><span class="sxs-lookup"><span data-stu-id="ac2f2-710">Renegotiation disabled</span></span>
* <span data-ttu-id="ac2f2-711">Compactação desabilitada</span><span class="sxs-lookup"><span data-stu-id="ac2f2-711">Compression disabled</span></span>
* <span data-ttu-id="ac2f2-712">Tamanhos mínimos de troca de chaves efêmera:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="ac2f2-713">Diffie-Hellman de curva elíptica (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits mínimo</span><span class="sxs-lookup"><span data-stu-id="ac2f2-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="ac2f2-714">Campo finito Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; mínimo de 2048 bits</span><span class="sxs-lookup"><span data-stu-id="ac2f2-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="ac2f2-715">Pacote de criptografia não autorizado</span><span class="sxs-lookup"><span data-stu-id="ac2f2-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="ac2f2-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; com a curva elíptica P-256 &lbrack;`FIPS186`&rbrack; tem suporte por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="ac2f2-717">O exemplo a seguir permite conexões HTTP/1.1 e HTTP/2 na porta 8000.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="ac2f2-718">As conexões são protegidas pela TLS com um certificado fornecido:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="ac2f2-719">Crie opcionalmente uma implementação do `IConnectionAdapter` para filtrar os handshakes TLS por conexão para codificações específicas:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="ac2f2-720">*Definir o protocolo com base na configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="ac2f2-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> chama `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="ac2f2-722">No exemplo *appsettings.json* a seguir, um protocolo de conexão padrão (HTTP/1.1 e HTTP/2) é estabelecido para todos os pontos de extremidade do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="ac2f2-723">O arquivo de configuração de exemplo a seguir estabelece um protocolo de conexão para um ponto de extremidade específico:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="ac2f2-724">Os protocolos especificados no código substituem os valores definidos pela configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ac2f2-725">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="ac2f2-725">Transport configuration</span></span>

<span data-ttu-id="ac2f2-726">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="ac2f2-727">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="ac2f2-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="ac2f2-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="ac2f2-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="ac2f2-730">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="ac2f2-731">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="ac2f2-732">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="ac2f2-733">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="ac2f2-733">URL prefixes</span></span>

<span data-ttu-id="ac2f2-734">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="ac2f2-735">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ac2f2-736">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="ac2f2-737">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ac2f2-738">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ac2f2-739">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ac2f2-740">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ac2f2-741">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ac2f2-742">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ac2f2-743">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ac2f2-744">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ac2f2-745">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="ac2f2-746">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ac2f2-747">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ac2f2-748">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ac2f2-749">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ac2f2-750">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="ac2f2-750">Host filtering</span></span>

<span data-ttu-id="ac2f2-751">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="ac2f2-752">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ac2f2-753">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ac2f2-754">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="ac2f2-755">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="ac2f2-756">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="ac2f2-757">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ac2f2-758">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="ac2f2-759">Para habilitar o middleware, defina uma chave `AllowedHosts` em *appSettings. JSON*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="ac2f2-760">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="ac2f2-761">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ac2f2-762">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="ac2f2-763">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="ac2f2-764">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="ac2f2-765">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="ac2f2-766">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ac2f2-767">O Kestrel é um [servidor Web multiplataforma para o ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ac2f2-768">O Kestrel é o servidor Web que está incluído por padrão em modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ac2f2-769">O Kestrel dá suporte aos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="ac2f2-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ac2f2-770">HTTPS</span></span>
* <span data-ttu-id="ac2f2-771">Atualização do Opaque usado para habilitar o [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="ac2f2-772">Soquetes do UNIX para alto desempenho protegidos pelo Nginx</span><span class="sxs-lookup"><span data-stu-id="ac2f2-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="ac2f2-773">Há suporte para o Kestrel em todas as plataformas e versões compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ac2f2-774">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac2f2-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ac2f2-775">Quando usar o Kestrel com um proxy reverso</span><span class="sxs-lookup"><span data-stu-id="ac2f2-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="ac2f2-776">O Kestrel pode ser usado sozinho ou com um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), o [Nginx](https://nginx.org) ou o [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ac2f2-777">Um servidor proxy reverso recebe solicitações HTTP da rede e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="ac2f2-778">Kestrel usado como um servidor Web de borda (voltado para a Internet):</span><span class="sxs-lookup"><span data-stu-id="ac2f2-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ac2f2-780">Kestrel usado em uma configuração de proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-780">Kestrel used in a reverse proxy configuration:</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ac2f2-782">A configuração, com ou sem um servidor proxy reverso, é uma configuração de hospedagem com suporte.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="ac2f2-783">O Kestrel usado como um servidor de borda sem um servidor proxy reverso não dá suporte ao compartilhamento do mesmo IP e da mesma porta entre vários processos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="ac2f2-784">Quando o Kestrel é configurado para escutar uma porta, ele manipula todo o tráfego dessa porta, independentemente dos cabeçalhos `Host` das solicitações.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="ac2f2-785">Um proxy reverso que pode compartilhar portas tem a capacidade de encaminhar solicitações ao Kestrel em um IP e em uma porta exclusivos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ac2f2-786">Mesmo se um servidor proxy reverso não for necessário, o uso de um servidor proxy reverso poderá ser uma boa opção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="ac2f2-787">Um proxy reverso:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-787">A reverse proxy:</span></span>

* <span data-ttu-id="ac2f2-788">Pode limitar a área da superfície pública exposta dos aplicativos que ele hospeda.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ac2f2-789">Fornece uma camada adicional de configuração e proteção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="ac2f2-790">Pode ser integrado melhor à infraestrutura existente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="ac2f2-791">Simplifica o balanceamento de carga e a configuração de comunicação segura (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="ac2f2-792">Somente o servidor proxy reverso requer um certificado X. 509 e esse servidor pode se comunicar com os servidores do aplicativo na rede interna usando HTTP simples.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="ac2f2-793">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ac2f2-794">Como usar o Kestrel em aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ac2f2-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="ac2f2-795">O pacote [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) está incluído no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ac2f2-796">Os modelos de projeto do ASP.NET Core usam o Kestrel por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="ac2f2-797">Em *Program.cs*, o código de modelo chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="ac2f2-798">Para fornecer configuração adicional após chamar `CreateDefaultBuilder`, chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="ac2f2-799">Para obter mais informações sobre `CreateDefaultBuilder` e criar o host, consulte a seção *configurar um host* do <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="ac2f2-800">Opções do Kestrel</span><span class="sxs-lookup"><span data-stu-id="ac2f2-800">Kestrel options</span></span>

<span data-ttu-id="ac2f2-801">O servidor Web do Kestrel tem opções de configuração de restrição especialmente úteis em implantações para a Internet.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="ac2f2-802">Defina restrições sobre a propriedade <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="ac2f2-803">A propriedade `Limits` contém uma instância da classe <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="ac2f2-804">Os exemplos a seguir usam o namespace <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="ac2f2-805">As opções de Kestrel, que são C# configuradas no código nos exemplos a seguir, também podem ser definidas usando um [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="ac2f2-806">Por exemplo, o provedor de configuração de arquivo pode carregar a configuração Kestrel de *appSettings. JSON* ou *appSettings. { Arquivo de ambiente}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ac2f2-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="ac2f2-807">Use **uma** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="ac2f2-808">Configurar Kestrel no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="ac2f2-809">Injetar uma instância do `IConfiguration` na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="ac2f2-810">O exemplo a seguir pressupõe que a configuração injetada seja atribuída à propriedade `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="ac2f2-811">Em `Startup.ConfigureServices`, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="ac2f2-812">Configure o Kestrel ao compilar o host:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="ac2f2-813">No *Program.cs*, carregue a seção `Kestrel` da configuração na configuração do Kestrel:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="ac2f2-814">Ambas as abordagens anteriores funcionam com qualquer [provedor de configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="ac2f2-815">Tempo limite de keep-alive</span><span class="sxs-lookup"><span data-stu-id="ac2f2-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ac2f2-816">Obtém ou define o [tempo limite de keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ac2f2-817">O padrão é de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="ac2f2-818">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="ac2f2-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ac2f2-819">O número máximo de conexões TCP abertas simultâneas pode ser definido para o aplicativo inteiro com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="ac2f2-820">Há um limite separado para conexões que foram atualizadas do HTTP ou HTTPS para outro protocolo (por exemplo, em uma solicitação do WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="ac2f2-821">Depois que uma conexão é atualizada, ela não é contada em relação ao limite de `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="ac2f2-822">O número máximo de conexões é ilimitado (nulo) por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ac2f2-823">Tamanho máximo do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ac2f2-824">O tamanho máximo do corpo da solicitação padrão é de 30.000.000 bytes, que equivale aproximadamente a 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="ac2f2-825">A abordagem recomendada para substituir o limite em um aplicativo ASP.NET Core MVC é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ac2f2-826">Aqui está um exemplo que mostra como configurar a restrição para o aplicativo em cada solicitação:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="ac2f2-827">Substitua a configuração em uma solicitação específica no middleware:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ac2f2-828">Uma exceção será gerada se o aplicativo configurar o limite em uma solicitação depois que o aplicativo for iniciado para ler a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ac2f2-829">Há uma propriedade `IsReadOnly` que indica se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ac2f2-830">Quando um aplicativo é executado [fora do processo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) por trás do [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module), o limite de tamanho do corpo da solicitação do Kestrel fica desabilitado, pois o IIS já define o limite.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ac2f2-831">Taxa de dados mínima do corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ac2f2-832">O Kestrel verifica a cada segundo se os dados estão sendo recebidos na taxa especificada em bytes/segundo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ac2f2-833">Se a taxa cair abaixo do mínimo, o tempo limite da conexão será atingido. O período de carência é a quantidade de tempo que o Kestrel dá ao cliente para aumentar sua taxa de envio até o mínimo; a taxa não é verificada durante esse período.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ac2f2-834">O período de cortesia ajuda a evitar a remoção de conexões que inicialmente enviam dados em uma taxa baixa devido ao início lento do TCP.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="ac2f2-835">A taxa mínima de padrão é de 240 bytes/segundo com um período de cortesia de 5 segundos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ac2f2-836">Uma taxa mínima também se aplica à resposta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ac2f2-837">O código para definir o limite de solicitação e o limite de resposta é o mesmo, exceto por ter `RequestBody` ou `Response` nos nomes da propriedade e da interface.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="ac2f2-838">Este é um exemplo que mostra como configurar as taxas mínima de dados em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="ac2f2-839">Tempo limite dos cabeçalhos de solicitação</span><span class="sxs-lookup"><span data-stu-id="ac2f2-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ac2f2-840">Obtém ou define a quantidade máxima de tempo que o servidor gasta recebendo cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="ac2f2-841">O padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="ac2f2-842">E/S síncrona</span><span class="sxs-lookup"><span data-stu-id="ac2f2-842">Synchronous IO</span></span>

<span data-ttu-id="ac2f2-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controla se a E/S síncrona é permitida para a solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="ac2f2-844">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="ac2f2-845">Um grande número de operações de E/S síncronas de bloqueio pode levar à privação de pool de thread, o que faz com que o aplicativo não responda.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-845">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="ac2f2-846">Habilite `AllowSynchronousIO` somente ao usar uma biblioteca em que não há suporte para E/S assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="ac2f2-847">O exemplo a seguir desativa a E/S síncrona:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-847">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="ac2f2-848">Para obter informações sobre outras opções e limites do Kestrel, confira:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ac2f2-849">Configuração de ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="ac2f2-849">Endpoint configuration</span></span>

<span data-ttu-id="ac2f2-850">Por padrão, o ASP.NET Core associa a:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ac2f2-851">`https://localhost:5001` (quando um certificado de desenvolvimento local está presente)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="ac2f2-852">Especificar URLs usando:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-852">Specify URLs using the:</span></span>

* <span data-ttu-id="ac2f2-853">A variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="ac2f2-854">O argumento de linha de comando `--urls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="ac2f2-855">A chave de configuração do host `urls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="ac2f2-856">O método de extensão `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="ac2f2-857">O valor fornecido usando essas abordagens pode ser um ou mais pontos de extremidade HTTP e HTTPS (HTTPS se houver um certificado padrão).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="ac2f2-858">Configure o valor como uma lista separada por ponto e vírgula (por exemplo, `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ac2f2-859">Veja mais informações sobre essas abordagens em [URLs de servidor](xref:fundamentals/host/web-host#server-urls) e [Substituir configuração](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ac2f2-860">Um certificado de desenvolvimento é criado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-860">A development certificate is created:</span></span>

* <span data-ttu-id="ac2f2-861">Quando o [SDK do .NET Core](/dotnet/core/sdk) está instalado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="ac2f2-862">A [ferramenta dev-certs](xref:aspnetcore-2.1#https) é usada para criar um certificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="ac2f2-863">Alguns navegadores exigem a concessão de permissão explícita para confiar no certificado de desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="ac2f2-864">Os modelos de projeto configuram aplicativos para execução em HTTPS por padrão e incluem o [redirecionamento de HTTPS e o suporte a HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ac2f2-865">Chame os métodos <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> ou <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> em <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> para configurar prefixos de URL e portas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="ac2f2-866">`UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` e a variável de ambiente `ASPNETCORE_URLS` também funcionam mas têm limitações que serão indicadas mais adiante nesta seção (um certificado padrão precisa estar disponível para a configuração do ponto de extremidade HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="ac2f2-867">configuração de `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ac2f2-868">ConfigureEndpointDefaults (ação\<ListenerOptions >)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="ac2f2-869">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade especificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ac2f2-870">Chamar `ConfigureEndpointDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="ac2f2-871">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de chamar <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ac2f2-872">ConfigureHttpsDefaults (ação\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ac2f2-873">Especifica uma `Action` de configuração a ser executada para cada ponto de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ac2f2-874">Chamar `ConfigureHttpsDefaults` várias vezes substitui as `Action`s pela última `Action` especificada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="ac2f2-875">Os pontos de extremidade criados chamando <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **antes** de chamar <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> não terão os padrões aplicados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="ac2f2-876">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="ac2f2-877">Cria um carregador de configuração para configurar o Kestrel que usa uma <xref:Microsoft.Extensions.Configuration.IConfiguration> como entrada.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="ac2f2-878">A configuração precisa estar no escopo da seção de configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ac2f2-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ac2f2-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="ac2f2-880">Configure o Kestrel para usar HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="ac2f2-881">Extensões `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="ac2f2-882">`UseHttps` &ndash; configurar o Kestrel para usar HTTPS com o certificado padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="ac2f2-883">Gera uma exceção quando não há nenhum certificado padrão configurado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="ac2f2-884">Parâmetros de `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="ac2f2-885">`filename` é o caminho e o nome do arquivo de um arquivo de certificado, relativo ao diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ac2f2-886">`password` é a senha necessária para acessar os dados do certificado X.509 .</span><span class="sxs-lookup"><span data-stu-id="ac2f2-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ac2f2-887">`configureOptions` é uma `Action` para configurar as `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="ac2f2-888">Retorna o `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ac2f2-889">`storeName` é o repositório de certificados do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="ac2f2-890">`subject` é o nome da entidade do certificado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="ac2f2-891">`allowInvalid` indica se certificados inválidos devem ser considerados, como os certificados autoassinados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="ac2f2-892">`location` é o local do repositório do qual o certificado deve ser carregado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="ac2f2-893">`serverCertificate` é o certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="ac2f2-894">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ac2f2-895">No mínimo, um certificado padrão precisa ser fornecido.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ac2f2-896">Configurações com suporte descritas a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-896">Supported configurations described next:</span></span>

* <span data-ttu-id="ac2f2-897">Nenhuma configuração</span><span class="sxs-lookup"><span data-stu-id="ac2f2-897">No configuration</span></span>
* <span data-ttu-id="ac2f2-898">Substituir o certificado padrão da configuração</span><span class="sxs-lookup"><span data-stu-id="ac2f2-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="ac2f2-899">Alterar os padrões no código</span><span class="sxs-lookup"><span data-stu-id="ac2f2-899">Change the defaults in code</span></span>

<span data-ttu-id="ac2f2-900">*Nenhuma configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-900">*No configuration*</span></span>

<span data-ttu-id="ac2f2-901">O Kestrel escuta em `http://localhost:5000` e em `https://localhost:5001` (se houver um certificado padrão disponível).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="ac2f2-902">*Substituir o certificado padrão da configuração*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="ac2f2-903">`CreateDefaultBuilder` chama `Configure(context.Configuration.GetSection("Kestrel"))` por padrão ao carregar a configuração do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="ac2f2-904">Há um esquema de definições de configurações de aplicativo HTTPS padrão disponível para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="ac2f2-905">Configure vários pontos de extremidade, incluindo URLs e os certificados a serem usados, por meio de um arquivo no disco ou de um repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="ac2f2-906">No exemplo de *appsettings.json* a seguir:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="ac2f2-907">Defina **AllowInvalid** como `true` para permitir o uso de certificados inválidos (por exemplo, os certificados autoassinados).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="ac2f2-908">Qualquer ponto de extremidade HTTPS que não especifique um certificado (**HttpsDefaultCert** no exemplo a seguir) retornará ao certificado definido em **certificados** > **padrão** ou ao certificado de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="ac2f2-909">Uma alternativa ao uso de **Caminho** e **Senha** para qualquer nó de certificado é especificar o certificado usando campos de repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="ac2f2-910">Por exemplo, o certificado **Certificados** > **Padrão** pode ser especificado como:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ac2f2-911">Observações do esquema:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-911">Schema notes:</span></span>

* <span data-ttu-id="ac2f2-912">Os nomes de pontos de extremidade diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ac2f2-913">Por exemplo, `HTTPS` e `Https` são válidos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ac2f2-914">O parâmetro `Url` é necessário para cada ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ac2f2-915">O formato desse parâmetro é o mesmo que o do parâmetro de configuração de `Urls` de nível superior, exceto que ele é limitado a um único valor.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="ac2f2-916">Esses pontos de extremidade substituem aqueles definidos na configuração de `Urls` de nível superior em vez de serem adicionados a eles.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="ac2f2-917">Os pontos de extremidade definidos no código por meio de `Listen` são acumulados com os pontos de extremidade definidos na seção de configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ac2f2-918">A seção `Certificate` é opcional.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="ac2f2-919">Se a seção `Certificate` não for especificada, os padrões definidos nos cenários anteriores serão usados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="ac2f2-920">Se não houver nenhum padrão disponível, o servidor gerará uma exceção e não poderá ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="ac2f2-921">A seção `Certificate` é compatível com os certificados de **Caminho**&ndash;**Senha** e **Entidade**&ndash;**Repositório**.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ac2f2-922">Qualquer número de pontos de extremidade pode ser definido dessa forma, contanto que eles não causem conflitos de porta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="ac2f2-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` retorna um `KestrelConfigurationLoader` com um método `.Endpoint(string name, listenOptions => { })` que pode ser usado para complementar as definições de um ponto de extremidade configurado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="ac2f2-924">`KestrelServerOptions.ConfigurationLoader` pode ser acessado diretamente para continuar Iterando no carregador existente, como aquele fornecido pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="ac2f2-925">A seção de configuração para cada ponto de extremidade está disponível nas opções no método `Endpoint` para que as configurações personalizadas possam ser lidas.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="ac2f2-926">Várias configurações podem ser carregadas chamando `options.Configure(context.Configuration.GetSection("{SECTION}"))` novamente com outra seção.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="ac2f2-927">Somente a última configuração será usada, a menos que `Load` seja chamado explicitamente nas instâncias anteriores.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="ac2f2-928">O metapacote não chama `Load`, portanto, sua seção de configuração padrão pode ser substituída.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ac2f2-929">O `KestrelConfigurationLoader` espelha a família de APIs `Listen` de `KestrelServerOptions` como sobrecargas de `Endpoint`, portanto, os pontos de extremidade de código e de configuração podem ser configurados no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ac2f2-930">Essas sobrecargas não usam nomes e consomem somente as definições padrão da configuração.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ac2f2-931">*Alterar os padrões no código*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-931">*Change the defaults in code*</span></span>

<span data-ttu-id="ac2f2-932">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` podem ser usados para alterar as configurações padrão de `ListenOptions` e `HttpsConnectionAdapterOptions`, incluindo a substituição do certificado padrão especificado no cenário anterior.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ac2f2-933">`ConfigureEndpointDefaults` e `ConfigureHttpsDefaults` devem ser chamados antes que qualquer ponto de extremidade seja configurado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="ac2f2-934">*Suporte do Kestrel para SNI*</span><span class="sxs-lookup"><span data-stu-id="ac2f2-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="ac2f2-935">A [SNI (Indicação de Nome de Servidor)](https://tools.ietf.org/html/rfc6066#section-3) pode ser usada para hospedar vários domínios no mesmo endereço IP e na mesma porta.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="ac2f2-936">Para que a SNI funcione, o cliente envia o nome do host da sessão segura para o servidor durante o handshake TLS para que o servidor possa fornecer o certificado correto.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="ac2f2-937">O cliente usa o certificado fornecido para a comunicação criptografada com o servidor durante a sessão segura que segue o handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="ac2f2-938">O Kestrel permite a SNI por meio do retorno de chamada do `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ac2f2-939">O retorno de chamada é invocado uma vez por conexão para permitir que o aplicativo inspecione o nome do host e selecione o certificado apropriado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ac2f2-940">O suporte para SNI requer:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-940">SNI support requires:</span></span>

* <span data-ttu-id="ac2f2-941">Em execução na estrutura de destino `netcoreapp2.1` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="ac2f2-942">No `net461` ou posterior, o retorno de chamada é invocado, mas o `name` é sempre `null`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="ac2f2-943">O `name` também será `null` se o cliente não fornecer o parâmetro de nome do host no handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="ac2f2-944">Todos os sites executados na mesma instância do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="ac2f2-945">Kestrel não é compatível com o compartilhamento de endereço IP e porta entre várias instâncias sem um proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="ac2f2-946">Log de conexão</span><span class="sxs-lookup"><span data-stu-id="ac2f2-946">Connection logging</span></span>

<span data-ttu-id="ac2f2-947">Chame <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> para emitir logs de nível de depuração para comunicação em nível de byte em uma conexão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="ac2f2-948">O log de conexões é útil para solucionar problemas em comunicação de nível baixo, como durante a criptografia TLS e atrás de proxies.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="ac2f2-949">Se `UseConnectionLogging` for colocado antes `UseHttps`, o tráfego criptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="ac2f2-950">Se `UseConnectionLogging` for colocado após `UseHttps`, o tráfego descriptografado será registrado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ac2f2-951">Associar a um soquete TCP</span><span class="sxs-lookup"><span data-stu-id="ac2f2-951">Bind to a TCP socket</span></span>

<span data-ttu-id="ac2f2-952">O método <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> é associado a um soquete TCP, e um lambda de opções permite a configuração do certificado X.509:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="ac2f2-953">O exemplo configura o HTTPS de um ponto de extremidade com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="ac2f2-954">Use a mesma API para definir outras configurações do Kestrel para pontos de extremidade específicos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ac2f2-955">Associar a um soquete do UNIX</span><span class="sxs-lookup"><span data-stu-id="ac2f2-955">Bind to a Unix socket</span></span>

<span data-ttu-id="ac2f2-956">Escute em um soquete do UNIX com <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> para um melhor desempenho com o Nginx, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="ac2f2-957">No arquivo Nginx confiuguration, defina o `server` > `location` > entrada de `proxy_pass` para `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="ac2f2-958">`{KESTREL SOCKET}` é o nome do soquete fornecido a <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (por exemplo, `kestrel-test.sock` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="ac2f2-959">Verifique se o soquete é gravável por Nginx (por exemplo, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="ac2f2-960">Porta 0</span><span class="sxs-lookup"><span data-stu-id="ac2f2-960">Port 0</span></span>

<span data-ttu-id="ac2f2-961">Quando o número da porta `0` for especificado, o Kestrel se associará dinamicamente a uma porta disponível.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ac2f2-962">O exemplo a seguir mostra como determinar a qual porta o Kestrel realmente se associou no runtime:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ac2f2-963">Quando o aplicativo é executado, a saída da janela do console indica a porta dinâmica na qual o aplicativo pode ser acessado:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ac2f2-964">Limitações</span><span class="sxs-lookup"><span data-stu-id="ac2f2-964">Limitations</span></span>

<span data-ttu-id="ac2f2-965">Configure pontos de extremidade com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ac2f2-966">O argumento de linha de comando `--urls`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="ac2f2-967">A chave de configuração do host `urls`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-967">`urls` host configuration key</span></span>
* <span data-ttu-id="ac2f2-968">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ac2f2-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ac2f2-969">Esses métodos são úteis para fazer com que o código funcione com servidores que não sejam o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="ac2f2-970">No entanto, esteja ciente das seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="ac2f2-971">O protocolo HTTPS não pode ser usado com essas abordagens, a menos que um certificado padrão seja fornecido na configuração do ponto de extremidade HTTPS (por exemplo, usando a configuração `KestrelServerOptions` ou um arquivo de configuração, como já foi mostrado neste tópico).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="ac2f2-972">Quando ambas as abordagens, `Listen` e `UseUrls`, são usadas ao mesmo tempo, os pontos de extremidade de `Listen` substituem os pontos de extremidade de `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ac2f2-973">Configuração de ponto de extremidade do IIS</span><span class="sxs-lookup"><span data-stu-id="ac2f2-973">IIS endpoint configuration</span></span>

<span data-ttu-id="ac2f2-974">Ao usar o IIS, as associações de URL para IIS substituem as associações definidas por `Listen` ou `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ac2f2-975">Para obter mais informações, confira o tópico [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ac2f2-976">Configuração de transporte</span><span class="sxs-lookup"><span data-stu-id="ac2f2-976">Transport configuration</span></span>

<span data-ttu-id="ac2f2-977">Com a liberação do ASP.NET Core 2.1, o transporte padrão do Kestrel deixa de ser baseado no Libuv, baseando-se agora em soquetes gerenciados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="ac2f2-978">Essa é uma alteração da falha para aplicativos ASP.NET Core 2.0 que atualizam para o 2.1 que chamam <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> e dependem de um dos seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="ac2f2-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (referência direta de pacote)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="ac2f2-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="ac2f2-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="ac2f2-981">Para projetos que exigem o uso de Libuv:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="ac2f2-982">Adicione uma dependência para o pacote [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="ac2f2-983">Chame <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="ac2f2-984">Prefixos de URL</span><span class="sxs-lookup"><span data-stu-id="ac2f2-984">URL prefixes</span></span>

<span data-ttu-id="ac2f2-985">Ao usar `UseUrls`, o argumento de linha de comando `--urls`, a chave de configuração de host `urls` ou a variável de ambiente `ASPNETCORE_URLS`, os prefixos de URL podem estar em um dos formatos a seguir.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="ac2f2-986">Somente os prefixos de URL HTTP são válidos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ac2f2-987">O Kestrel não é compatível com HTTPS ao configurar associações de URL que usam `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="ac2f2-988">Endereço IPv4 com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ac2f2-989">`0.0.0.0` é um caso especial que associa a todos os endereços IPv4.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ac2f2-990">Endereço IPv6 com número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ac2f2-991">`[::]` é o equivalente do IPv6 ao IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ac2f2-992">Nome do host com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ac2f2-993">Os nomes de host `*` e `+` não são especiais.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ac2f2-994">Tudo o que não é reconhecido como um endereço IP ou um `localhost` válido é associado a todos os IPs IPv6 e IPv4.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ac2f2-995">Para associar nomes de host diferentes a diferentes aplicativos ASP.NET Core na mesma porta, use o [HTTP.sys](xref:fundamentals/servers/httpsys) ou um servidor proxy reverso, como o IIS, o Nginx ou o Apache.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ac2f2-996">A hospedagem em uma configuração de proxy reverso exige a [filtragem de host](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="ac2f2-997">Nome do `localhost` do host com o número da porta ou o IP de loopback com o número da porta</span><span class="sxs-lookup"><span data-stu-id="ac2f2-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ac2f2-998">Quando o `localhost` for especificado, o Kestrel tentará se associar às interfaces de loopback IPv4 e IPv6.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ac2f2-999">Se a porta solicitada está sendo usada por outro serviço em uma das interfaces de loopback, o Kestrel falha ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ac2f2-1000">Se uma das interfaces de loopback não estiver disponível por qualquer outro motivo (geralmente porque não há suporte para o IPv6), o Kestrel registra um aviso em log.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ac2f2-1001">Filtragem de host</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1001">Host filtering</span></span>

<span data-ttu-id="ac2f2-1002">Embora o Kestrel permita a configuração com base em prefixos como `http://example.com:5000`, ele geralmente ignora o nome do host.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="ac2f2-1003">O host `localhost` é um caso especial usado para a associação a endereços de loopback.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ac2f2-1004">Todo host que não for um endereço IP explícito será associado a todos os endereços IP públicos.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ac2f2-1005">Cabeçalhos `Host` não são validados.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="ac2f2-1006">Como uma solução alternativa, use o Middleware de Filtragem de Host.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="ac2f2-1007">O middleware de filtragem de host é fornecido pelo pacote [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , que está incluído no [metapacote Microsoft. AspNetCore. App](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 ou 2,2).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="ac2f2-1008">O middleware é adicionado pelo <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, que chama <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ac2f2-1009">Middleware de Filtragem de Host está desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="ac2f2-1010">Para habilitar o middleware, defina uma chave `AllowedHosts` em *appSettings. JSON*/*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="ac2f2-1011">O valor dessa chave é uma lista separada por ponto e vírgula de nomes de host sem números de porta:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="ac2f2-1012">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ac2f2-1013">[Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) também tem uma opção <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="ac2f2-1014">Middleware de Cabeçalhos Encaminhados e Middleware de filtragem de Host têm funcionalidades semelhantes para cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="ac2f2-1015">A definição de `AllowedHosts` com Middleware de Cabeçalhos Encaminhados é apropriada quando o cabeçalho `Host` não é preservado ao encaminhar solicitações com um servidor proxy reverso ou um balanceador de carga.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="ac2f2-1016">A definição de `AllowedHosts` com Middleware de Filtragem de Host é apropriada quando o Kestrel é usado como um servidor de borda voltado ao público ou quando o cabeçalho `Host` é encaminhado diretamente.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="ac2f2-1017">Para obter mais informações sobre o Middleware de Cabeçalhos Encaminhados, confira <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ac2f2-1018">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1018">Additional resources</span></span>

* <span data-ttu-id="ac2f2-1019">Ao usar soquetes UNIX no Linux, o soquete não é excluído automaticamente no aplicativo desligado.</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1019">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="ac2f2-1020">Saiba mais neste [tópico do GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1020">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="ac2f2-1021">RFC 7230: Sintaxe e roteamento da mensagem (Seção 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="ac2f2-1021">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
