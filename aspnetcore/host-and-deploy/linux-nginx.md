---
title: Host ASP.NET Core no Linux com Nginx
author: guardrex
description: Saiba como configurar o Nginx como um proxy reverso no Ubuntu 16.04 para encaminhar o tráfego HTTP para um aplicativo Web ASP.NET Core em execução no Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/02/2019
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: f307a1c3e0dc62c5dc03e50d710696fadd9fd487
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717384"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="27ece-103">Host ASP.NET Core no Linux com Nginx</span><span class="sxs-lookup"><span data-stu-id="27ece-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="27ece-104">Por [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="27ece-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="27ece-105">Este guia explica como configurar um ambiente ASP.NET Core pronto para produção em um servidor Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="27ece-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="27ece-106">Essas instruções provavelmente funcionarão com versões mais recentes do Ubuntu, mas as instruções não foram testadas com versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="27ece-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="27ece-107">Para saber mais sobre outras distribuições do Linux compatíveis com o ASP.NET Core, veja [Pré-requisitos para o .NET Core no Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="27ece-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="27ece-108">Para Ubuntu 14.04, o *supervisord* é recomendado como uma solução para monitorar o processo do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="27ece-108">For Ubuntu 14.04, *supervisord* is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="27ece-109">O *systemd* não está disponível no Ubuntu 14.04.</span><span class="sxs-lookup"><span data-stu-id="27ece-109">*systemd* isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="27ece-110">Para obter instruções Ubuntu 14.04, veja a [versão anterior deste tópico](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="27ece-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/aspnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="27ece-111">Este guia:</span><span class="sxs-lookup"><span data-stu-id="27ece-111">This guide:</span></span>

* <span data-ttu-id="27ece-112">Coloca um aplicativo ASP.NET Core existente em um servidor proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="27ece-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="27ece-113">Configura o servidor proxy reverso para encaminhar solicitações ao servidor Web do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="27ece-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="27ece-114">Assegura que o aplicativo Web seja executado na inicialização como um daemon.</span><span class="sxs-lookup"><span data-stu-id="27ece-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="27ece-115">Configura uma ferramenta de gerenciamento de processo para ajudar a reiniciar o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="27ece-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="27ece-116">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="27ece-116">Prerequisites</span></span>

1. <span data-ttu-id="27ece-117">Acesso a um servidor Ubuntu 16.04 com uma conta de usuário padrão com privilégio sudo.</span><span class="sxs-lookup"><span data-stu-id="27ece-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="27ece-118">Instale o runtime do .NET Core no servidor.</span><span class="sxs-lookup"><span data-stu-id="27ece-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="27ece-119">Visite a [página baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="27ece-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="27ece-120">Selecione a versão mais recente do .NET Core sem visualização.</span><span class="sxs-lookup"><span data-stu-id="27ece-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="27ece-121">Baixe o tempo de execução de não visualização mais recente na tabela em **executar aplicativos-tempo de execução**.</span><span class="sxs-lookup"><span data-stu-id="27ece-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="27ece-122">Selecione o link de **instruções do Gerenciador de pacotes** do Linux e siga as instruções do Ubuntu para sua versão do Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="27ece-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="27ece-123">Um aplicativo ASP.NET Core existente.</span><span class="sxs-lookup"><span data-stu-id="27ece-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="27ece-124">A qualquer momento no futuro depois de atualizar a estrutura compartilhada, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="27ece-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="27ece-125">Publicar e copiar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="27ece-125">Publish and copy over the app</span></span>

<span data-ttu-id="27ece-126">Configurar o aplicativo para um [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="27ece-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="27ece-127">Se o aplicativo for executado localmente e não estiver configurado para fazer conexões seguras (HTTPS), adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="27ece-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="27ece-128">Configure o aplicativo para lidar com conexões seguras locais.</span><span class="sxs-lookup"><span data-stu-id="27ece-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="27ece-129">Para obter mais informações, veja a seção [Configuração de HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="27ece-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="27ece-130">Remova `https://localhost:5001` (se houver) da propriedade `applicationUrl` no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="27ece-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="27ece-131">Execute [dotnet publish](/dotnet/core/tools/dotnet-publish) do ambiente de desenvolvimento para empacotar um aplicativo em um diretório (por exemplo, *bin/Release/&lt;target_framework_moniker&gt;/publish*) que pode ser executado no servidor:</span><span class="sxs-lookup"><span data-stu-id="27ece-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="27ece-132">O aplicativo também poderá ser publicado como uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd) se você preferir não manter o runtime do .NET Core no servidor.</span><span class="sxs-lookup"><span data-stu-id="27ece-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="27ece-133">Copie o aplicativo ASP.NET Core para o servidor usando uma ferramenta que se integre ao fluxo de trabalho da organização (por exemplo, SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="27ece-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="27ece-134">É comum para localizar os aplicativos Web no diretório *var* (por exemplo, *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="27ece-134">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="27ece-135">Em um cenário de implantação de produção, um fluxo de trabalho de integração contínua faz o trabalho de publicar o aplicativo e copiar os ativos para o servidor.</span><span class="sxs-lookup"><span data-stu-id="27ece-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="27ece-136">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="27ece-136">Test the app:</span></span>

1. <span data-ttu-id="27ece-137">Na linha de comando, execute o aplicativo: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="27ece-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="27ece-138">Em um navegador, vá para `http://<serveraddress>:<port>` para verificar se o aplicativo funciona no Linux localmente.</span><span class="sxs-lookup"><span data-stu-id="27ece-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="27ece-139">Configurar um servidor proxy reverso</span><span class="sxs-lookup"><span data-stu-id="27ece-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="27ece-140">Um proxy reverso é uma configuração comum para atender a aplicativos Web dinâmicos.</span><span class="sxs-lookup"><span data-stu-id="27ece-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="27ece-141">Um proxy reverso encerra a solicitação HTTP e a encaminha para o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27ece-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="27ece-142">Usar um servidor proxy reverso</span><span class="sxs-lookup"><span data-stu-id="27ece-142">Use a reverse proxy server</span></span>

<span data-ttu-id="27ece-143">O Kestrel é excelente para servir conteúdo dinâmico do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27ece-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="27ece-144">No entanto, as funcionalidades de servidor Web não têm tantos recursos quanto servidores como IIS, Apache ou Nginx.</span><span class="sxs-lookup"><span data-stu-id="27ece-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="27ece-145">Um servidor proxy reverso pode descarregar trabalho como servir conteúdo estático, armazenar solicitações em cache, compactar solicitações e terminar HTTPS do servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="27ece-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="27ece-146">Um servidor proxy reverso pode residir em um computador dedicado ou pode ser implantado junto com um servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="27ece-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="27ece-147">Para os fins deste guia, uma única instância de Nginx é usada.</span><span class="sxs-lookup"><span data-stu-id="27ece-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="27ece-148">Ela é executada no mesmo servidor, junto com o servidor HTTP.</span><span class="sxs-lookup"><span data-stu-id="27ece-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="27ece-149">Com base nos requisitos, uma configuração diferente pode ser escolhida.</span><span class="sxs-lookup"><span data-stu-id="27ece-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="27ece-150">Uma vez que as solicitações são encaminhadas pelo proxy reverso, use o [Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/).</span><span class="sxs-lookup"><span data-stu-id="27ece-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="27ece-151">O middleware atualiza o `Request.Scheme` usando o cabeçalho `X-Forwarded-Proto`, de forma que URIs de redirecionamento e outras políticas de segurança funcionam corretamente.</span><span class="sxs-lookup"><span data-stu-id="27ece-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="27ece-152">Qualquer componente que dependa do esquema, como autenticação, geração de link, redirecionamentos e localização geográfica, deverá ser colocado depois de invocar o Middleware de Cabeçalhos Encaminhados.</span><span class="sxs-lookup"><span data-stu-id="27ece-152">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="27ece-153">Como regra geral, o Middleware de Cabeçalhos Encaminhados deve ser executado antes de outro middleware, exceto middleware de tratamento de erro e de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="27ece-153">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="27ece-154">Essa ordenação garantirá que o middleware conte com informações de cabeçalhos encaminhadas que podem consumir os valores de cabeçalho para processamento.</span><span class="sxs-lookup"><span data-stu-id="27ece-154">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="27ece-155">Invoque o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure` antes de chamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ou outro middleware de esquema de autenticação semelhante.</span><span class="sxs-lookup"><span data-stu-id="27ece-155">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="27ece-156">Configure o middleware para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="27ece-156">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="27ece-157">Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, os cabeçalhos padrão para encaminhar serão `None`.</span><span class="sxs-lookup"><span data-stu-id="27ece-157">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="27ece-158">Os proxies em execução em endereços de loopback (127.0.0.0/8, [::1]), incluindo o endereço de host local padrão (127.0.0.1), são confiáveis por padrão.</span><span class="sxs-lookup"><span data-stu-id="27ece-158">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="27ece-159">Se outros proxies ou redes confiáveis em que a organização trata solicitações entre a Internet e o servidor Web, adicione-os à lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="27ece-159">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="27ece-160">O exemplo a seguir adiciona um servidor proxy confiável no endereço IP 10.0.0.100 ao Middleware de cabeçalhos encaminhados `KnownProxies` em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="27ece-160">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="27ece-161">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="27ece-161">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="27ece-162">Instalar o Nginx</span><span class="sxs-lookup"><span data-stu-id="27ece-162">Install Nginx</span></span>

<span data-ttu-id="27ece-163">Use `apt-get` para instalar o Nginx.</span><span class="sxs-lookup"><span data-stu-id="27ece-163">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="27ece-164">O instalador cria um script de inicialização *systemd* que executa o Nginx como daemon na inicialização do sistema.</span><span class="sxs-lookup"><span data-stu-id="27ece-164">The installer creates a *systemd* init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="27ece-165">Siga as instruções de instalação para o Ubuntu em [Nginx: pacotes Debian/Ubuntu oficiais](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="27ece-165">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="27ece-166">Se módulos Nginx opcionais forem exigidos, poderá haver necessidade de criar o Nginx da origem.</span><span class="sxs-lookup"><span data-stu-id="27ece-166">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="27ece-167">Já que Nginx foi instalado pela primeira vez, inicie-o explicitamente executando:</span><span class="sxs-lookup"><span data-stu-id="27ece-167">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="27ece-168">Verifique se um navegador exibe a página de aterrissagem padrão do Nginx.</span><span class="sxs-lookup"><span data-stu-id="27ece-168">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="27ece-169">A página de aterrissagem é acessível em `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="27ece-169">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="27ece-170">Configurar o Nginx</span><span class="sxs-lookup"><span data-stu-id="27ece-170">Configure Nginx</span></span>

<span data-ttu-id="27ece-171">Para configurar o Nginx como um proxy inverso para encaminhar solicitações para o nosso aplicativo ASP.NET Core, modifique */etc/nginx/sites-available/default*.</span><span class="sxs-lookup"><span data-stu-id="27ece-171">To configure Nginx as a reverse proxy to forward requests to your ASP.NET Core app, modify */etc/nginx/sites-available/default*.</span></span> <span data-ttu-id="27ece-172">Abra-o em um editor de texto arquivo e substitua o conteúdo pelo mostrado a seguir:</span><span class="sxs-lookup"><span data-stu-id="27ece-172">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="27ece-173">Quando nenhum `server_name` corresponde, o Nginx usa o servidor padrão.</span><span class="sxs-lookup"><span data-stu-id="27ece-173">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="27ece-174">Se nenhum servidor padrão é definido, o primeiro servidor no arquivo de configuração é o servidor padrão.</span><span class="sxs-lookup"><span data-stu-id="27ece-174">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="27ece-175">Como prática recomendada, adicione um servidor padrão específico que retorna um código de status 444 no arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="27ece-175">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="27ece-176">Um exemplo de configuração de servidor padrão é:</span><span class="sxs-lookup"><span data-stu-id="27ece-176">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="27ece-177">Com o servidor padrão e o arquivo de configuração anterior, o Nginx aceita tráfego público na porta 80 com um cabeçalho de host `example.com` ou `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="27ece-177">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="27ece-178">Solicitações que não correspondam a esses hosts não serão encaminhadas para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="27ece-178">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="27ece-179">O Nginx encaminha as solicitações correspondentes para o Kestrel em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="27ece-179">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="27ece-180">Veja [Como o nginx processa uma solicitação](https://nginx.org/docs/http/request_processing.html) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="27ece-180">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="27ece-181">Para alterar o IP/porta do Kestrel, veja [Kestrel: configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="27ece-181">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="27ece-182">Falha ao especificar uma [diretiva server_name](https://nginx.org/docs/http/server_names.html) expõe seu aplicativo para vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="27ece-182">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="27ece-183">Associações de curinga de subdomínio (por exemplo, `*.example.com`) não oferecerão esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="27ece-183">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="27ece-184">Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="27ece-184">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="27ece-185">Quando a configuração Nginx é estabelecida, execute `sudo nginx -t` para verificar a sintaxe dos arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="27ece-185">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="27ece-186">Se o teste do arquivo de configuração for bem-sucedido, você poderá forçar o Nginx a acompanhar as alterações executando `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="27ece-186">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="27ece-187">Para executar o aplicativo diretamente no servidor:</span><span class="sxs-lookup"><span data-stu-id="27ece-187">To directly run the app on the server:</span></span>

1. <span data-ttu-id="27ece-188">Navegue até o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27ece-188">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="27ece-189">Execute o aplicativo: `dotnet <app_assembly.dll>`, em que `app_assembly.dll` é o nome do arquivo do assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27ece-189">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="27ece-190">Se o aplicativo for executado no servidor, mas não responder pela Internet, verifique o firewall do servidor e confirme que a porta 80 está aberta.</span><span class="sxs-lookup"><span data-stu-id="27ece-190">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="27ece-191">Se você estiver usando uma VM do Azure Ubuntu, adicione uma regra NSG (Grupo de Segurança de Rede) que permite tráfego de entrada na porta 80.</span><span class="sxs-lookup"><span data-stu-id="27ece-191">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="27ece-192">Não é necessário habilitar uma regra de saída da porta 80, uma vez que o tráfego de saída é concedido automaticamente quando a regra de entrada é habilitada.</span><span class="sxs-lookup"><span data-stu-id="27ece-192">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="27ece-193">Quando terminar de testar o aplicativo, encerre o aplicativo com `Ctrl+C` no prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="27ece-193">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="27ece-194">Monitorar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="27ece-194">Monitor the app</span></span>

<span data-ttu-id="27ece-195">O servidor agora está configurado para encaminhar solicitações feitas a `http://<serveraddress>:80` ao aplicativo ASP.NET Core em execução no Kestrel em `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="27ece-195">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="27ece-196">No entanto, o Nginx não está configurado para gerenciar o processo do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="27ece-196">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="27ece-197">É possível usar o *systemd* para criar um arquivo de serviço para iniciar e monitorar o aplicativo Web subjacente.</span><span class="sxs-lookup"><span data-stu-id="27ece-197">*systemd* can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="27ece-198">*systemd* é um sistema de inicialização que fornece muitos recursos poderosos para iniciar, parar e gerenciar processos.</span><span class="sxs-lookup"><span data-stu-id="27ece-198">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="27ece-199">Criar o arquivo de serviço</span><span class="sxs-lookup"><span data-stu-id="27ece-199">Create the service file</span></span>

<span data-ttu-id="27ece-200">Crie o arquivo de definição de serviço:</span><span class="sxs-lookup"><span data-stu-id="27ece-200">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="27ece-201">A seguir, um exemplo de arquivo de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="27ece-201">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="27ece-202">Se o usuário *www-data* não é usado pela configuração, o usuário definido aqui deve ser criado primeiro e a propriedade adequada dos arquivos deve ser concedida a ele.</span><span class="sxs-lookup"><span data-stu-id="27ece-202">If the user *www-data* isn't used by the configuration, the user defined here must be created first and given proper ownership for files.</span></span>

<span data-ttu-id="27ece-203">Use `TimeoutStopSec` para configurar a duração do tempo de espera para o aplicativo desligar depois de receber o sinal de interrupção inicial.</span><span class="sxs-lookup"><span data-stu-id="27ece-203">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="27ece-204">Se o aplicativo não desligar nesse período, o SIGKILL será emitido para encerrá-lo.</span><span class="sxs-lookup"><span data-stu-id="27ece-204">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="27ece-205">Forneça o valor como segundos sem unidade (por exemplo, `150`), um valor de duração (por exemplo, `2min 30s`) ou `infinity` para desabilitar o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="27ece-205">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="27ece-206">`TimeoutStopSec` é revertido para o valor padrão de `DefaultTimeoutStopSec` no arquivo de configuração do gerenciador (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf* e *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="27ece-206">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="27ece-207">O tempo limite padrão para a maioria das distribuições é de 90 segundos.</span><span class="sxs-lookup"><span data-stu-id="27ece-207">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="27ece-208">O Linux tem um sistema de arquivos que diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="27ece-208">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="27ece-209">Definir ASPNETCORE_ENVIRONMENT para "Production" resulta em uma pesquisa pelo arquivo de configuração *appsettings.Production.json*, e não *appsettings.production.json*.</span><span class="sxs-lookup"><span data-stu-id="27ece-209">Setting ASPNETCORE_ENVIRONMENT to "Production" results in searching for the configuration file *appsettings.Production.json*, not *appsettings.production.json*.</span></span>

<span data-ttu-id="27ece-210">Alguns valores (por exemplo, cadeias de conexão de SQL) devem ser escapadas para que os provedores de configuração leiam as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="27ece-210">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="27ece-211">Use o seguinte comando para gerar um valor corretamente com caracteres de escape para uso no arquivo de configuração:</span><span class="sxs-lookup"><span data-stu-id="27ece-211">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="27ece-212">Separadores do tipo dois-pontos (`:`) não são compatíveis com nomes de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="27ece-212">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="27ece-213">Use um sublinhado duplo (`__`) no lugar de dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="27ece-213">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="27ece-214">O [provedor de configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converte caracteres de sublinhado duplo em dois-pontos quando as variáveis de ambiente são lidas na configuração.</span><span class="sxs-lookup"><span data-stu-id="27ece-214">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="27ece-215">No exemplo a seguir, a chave de cadeia de conexão `ConnectionStrings:DefaultConnection` está definida no arquivo de definição de serviço como `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="27ece-215">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="27ece-216">Salve o arquivo e habilite o serviço.</span><span class="sxs-lookup"><span data-stu-id="27ece-216">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="27ece-217">Inicie o serviço e verifique se ele está em execução.</span><span class="sxs-lookup"><span data-stu-id="27ece-217">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="27ece-218">Com o proxy reverso configurado e o Kestrel gerenciado por meio de systemd, o aplicativo Web está totalmente configurado e pode ser acessado em um navegador no computador local em `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="27ece-218">With the reverse proxy configured and Kestrel managed through systemd, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="27ece-219">Ele também é acessível por meio de um computador remoto, bloqueando qualquer firewall que o possa estar bloqueando.</span><span class="sxs-lookup"><span data-stu-id="27ece-219">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="27ece-220">Inspecionando os cabeçalhos de resposta, o cabeçalho `Server` mostra o aplicativo ASP.NET Core sendo servido pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="27ece-220">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="27ece-221">Exibir logs</span><span class="sxs-lookup"><span data-stu-id="27ece-221">View logs</span></span>

<span data-ttu-id="27ece-222">Já que o aplicativo Web usando Kestrel é gerenciado usando `systemd`, todos os eventos e os processos são registrados em um diário centralizado.</span><span class="sxs-lookup"><span data-stu-id="27ece-222">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="27ece-223">No entanto, esse diário contém todas as entradas para todos os serviços e processos gerenciados pelo `systemd`.</span><span class="sxs-lookup"><span data-stu-id="27ece-223">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="27ece-224">Para exibir os itens específicos de `kestrel-helloapp.service`, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="27ece-224">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="27ece-225">Para obter mais filtragem, opções de tempo como `--since today`, `--until 1 hour ago` ou uma combinação desses fatores pode reduzir a quantidade de entradas retornadas.</span><span class="sxs-lookup"><span data-stu-id="27ece-225">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="27ece-226">Proteção de dados</span><span class="sxs-lookup"><span data-stu-id="27ece-226">Data protection</span></span>

<span data-ttu-id="27ece-227">A [pilha de proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) do ASP.NET Core, incluindo o middleware de autenticação (por exemplo, o middleware de cookie) e as proteções de CSRF (solicitação intersite forjada).</span><span class="sxs-lookup"><span data-stu-id="27ece-227">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="27ece-228">Mesmo se as APIs de Proteção de Dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente.</span><span class="sxs-lookup"><span data-stu-id="27ece-228">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="27ece-229">Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="27ece-229">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="27ece-230">Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:</span><span class="sxs-lookup"><span data-stu-id="27ece-230">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="27ece-231">Todos os tokens de autenticação baseados em cookies serão invalidados.</span><span class="sxs-lookup"><span data-stu-id="27ece-231">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="27ece-232">Os usuários precisam entrar novamente na próxima solicitação deles.</span><span class="sxs-lookup"><span data-stu-id="27ece-232">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="27ece-233">Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados.</span><span class="sxs-lookup"><span data-stu-id="27ece-233">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="27ece-234">Isso pode incluir os [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [cookies TempData do MVC do ASP.NET Core](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="27ece-234">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="27ece-235">Para configurar a proteção de dados de modo que ela mantenha e criptografe o token de autenticação, consulte:</span><span class="sxs-lookup"><span data-stu-id="27ece-235">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="27ece-236">Campos de cabeçalho da solicitação muito grandes</span><span class="sxs-lookup"><span data-stu-id="27ece-236">Long request header fields</span></span>

<span data-ttu-id="27ece-237">As configurações padrão do servidor proxy normalmente limitam os campos de cabeçalho de solicitação a 4 K ou 8 K, dependendo da plataforma.</span><span class="sxs-lookup"><span data-stu-id="27ece-237">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="27ece-238">Um aplicativo pode exigir campos maiores do que o padrão (por exemplo, aplicativos que usam [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="27ece-238">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="27ece-239">Se forem necessários campos mais longos, as configurações padrão do servidor proxy exigirão ajuste.</span><span class="sxs-lookup"><span data-stu-id="27ece-239">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="27ece-240">Os valores a serem aplicados dependem do cenário.</span><span class="sxs-lookup"><span data-stu-id="27ece-240">The values to apply depend on the scenario.</span></span> <span data-ttu-id="27ece-241">Para obter mais informações, confira a documentação do servidor.</span><span class="sxs-lookup"><span data-stu-id="27ece-241">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="27ece-242">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="27ece-242">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="27ece-243">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="27ece-243">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="27ece-244">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="27ece-244">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="27ece-245">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="27ece-245">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="27ece-246">Não aumente os valores padrão de buffers de proxy a menos que necessário.</span><span class="sxs-lookup"><span data-stu-id="27ece-246">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="27ece-247">Aumentar esses valores aumenta o risco de estouro de buffer (estouro) e ataques de DoS (negação de serviço) por usuários mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="27ece-247">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="27ece-248">Proteger o aplicativo</span><span class="sxs-lookup"><span data-stu-id="27ece-248">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="27ece-249">Habilitar AppArmor</span><span class="sxs-lookup"><span data-stu-id="27ece-249">Enable AppArmor</span></span>

<span data-ttu-id="27ece-250">O LSM (Módulos de Segurança do Linux) é uma estrutura que é parte do kernel do Linux desde o Linux 2.6.</span><span class="sxs-lookup"><span data-stu-id="27ece-250">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="27ece-251">O LSM dá suporte a diferentes implementações de módulos de segurança.</span><span class="sxs-lookup"><span data-stu-id="27ece-251">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="27ece-252">O [AppArmor](https://wiki.ubuntu.com/AppArmor) é um LSM que implementa um sistema de controle de acesso obrigatório que permite restringir o programa a um conjunto limitado de recursos.</span><span class="sxs-lookup"><span data-stu-id="27ece-252">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="27ece-253">Verifique se o AppArmor está habilitado e configurado corretamente.</span><span class="sxs-lookup"><span data-stu-id="27ece-253">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="27ece-254">Configurar o firewall</span><span class="sxs-lookup"><span data-stu-id="27ece-254">Configure the firewall</span></span>

<span data-ttu-id="27ece-255">Feche todas as portas externas que não estão em uso.</span><span class="sxs-lookup"><span data-stu-id="27ece-255">Close off all external ports that are not in use.</span></span> <span data-ttu-id="27ece-256">Firewall descomplicado (ufw) fornece um front-end para `iptables` fornecendo uma interface de linha de comando para configurar o firewall.</span><span class="sxs-lookup"><span data-stu-id="27ece-256">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a command line interface for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="27ece-257">Um firewall impedirá o acesso a todo o sistema se não ele estiver configurado corretamente.</span><span class="sxs-lookup"><span data-stu-id="27ece-257">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="27ece-258">Se houver falha ao especificar a porta SSH correta, você será bloqueado do sistema se estiver usando o SSH para se conectar a ele.</span><span class="sxs-lookup"><span data-stu-id="27ece-258">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="27ece-259">A porta padrão é a 22.</span><span class="sxs-lookup"><span data-stu-id="27ece-259">The default port is 22.</span></span> <span data-ttu-id="27ece-260">Para obter mais informações, consulte a [introdução ao ufw](https://help.ubuntu.com/community/UFW) e o [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="27ece-260">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="27ece-261">Instale o `ufw` e configure-o para permitir o tráfego em todas as portas necessárias.</span><span class="sxs-lookup"><span data-stu-id="27ece-261">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="27ece-262">Proteger o Nginx</span><span class="sxs-lookup"><span data-stu-id="27ece-262">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="27ece-263">Alterar o nome da resposta do Nginx</span><span class="sxs-lookup"><span data-stu-id="27ece-263">Change the Nginx response name</span></span>

<span data-ttu-id="27ece-264">Edite *src/http/ngx_http_header_filter_module.c*:</span><span class="sxs-lookup"><span data-stu-id="27ece-264">Edit *src/http/ngx_http_header_filter_module.c*:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="27ece-265">Configurar opções</span><span class="sxs-lookup"><span data-stu-id="27ece-265">Configure options</span></span>

<span data-ttu-id="27ece-266">Configure o servidor com os módulos adicionais necessários.</span><span class="sxs-lookup"><span data-stu-id="27ece-266">Configure the server with additional required modules.</span></span> <span data-ttu-id="27ece-267">Considere usar um firewall de aplicativo Web como [ModSecurity](https://www.modsecurity.org/) para fortalecer o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27ece-267">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="27ece-268">Configuração de HTTPS</span><span class="sxs-lookup"><span data-stu-id="27ece-268">HTTPS configuration</span></span>

<span data-ttu-id="27ece-269">**Configurar o aplicativo para conexões seguras (HTTPS) locais**</span><span class="sxs-lookup"><span data-stu-id="27ece-269">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="27ece-270">O comando [dotnet run](/dotnet/core/tools/dotnet-run) usa o arquivo *Properties/launchSettings.json* do aplicativo, que configura o aplicativo para escutar nas URLs fornecidas pela propriedade `applicationUrl` (por exemplo, `https://localhost:5001; http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="27ece-270">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="27ece-271">Configure o aplicativo para usar um certificado no desenvolvimento para o comando `dotnet run` ou no ambiente de desenvolvimento (F5 ou Ctrl + F5 no Visual Studio Code) usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="27ece-271">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="27ece-272">[Substituir o certificado padrão da configuração](xref:fundamentals/servers/kestrel#configuration) (*Recomendado*)</span><span class="sxs-lookup"><span data-stu-id="27ece-272">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="27ece-273">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="27ece-273">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="27ece-274">**Configurar o proxy reverso para conexões de cliente seguras (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="27ece-274">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="27ece-275">Configure o servidor para escutar tráfego HTTPS na porta `443` especificando um certificado válido emitido por uma AC (autoridade de certificação) confiável.</span><span class="sxs-lookup"><span data-stu-id="27ece-275">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="27ece-276">Aprimore a segurança, empregando algumas das práticas descritas no arquivo */etc/nginx/nginx.conf* a seguir.</span><span class="sxs-lookup"><span data-stu-id="27ece-276">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="27ece-277">Exemplos incluem a escolha de uma criptografia mais forte e o redirecionamento de todo o tráfego por meio de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="27ece-277">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

* <span data-ttu-id="27ece-278">A adição de um cabeçalho `HTTP Strict-Transport-Security` (HSTS) garante que todas as próximas solicitações feitas pelo cliente sejam por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="27ece-278">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

* <span data-ttu-id="27ece-279">Não adicione o cabeçalho HSTS ou escolha um `max-age` apropriado, se quiser desabilitar o HTTPS futuramente.</span><span class="sxs-lookup"><span data-stu-id="27ece-279">Don't add the HSTS header or chose an appropriate `max-age` if HTTPS will be disabled in the future.</span></span>

<span data-ttu-id="27ece-280">Adicione o arquivo de configuração */etc/nginx/proxy.conf*:</span><span class="sxs-lookup"><span data-stu-id="27ece-280">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="27ece-281">Edite o arquivo de configuração */etc/nginx/nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="27ece-281">Edit the */etc/nginx/nginx.conf* configuration file.</span></span> <span data-ttu-id="27ece-282">O exemplo contém ambas as seções `http` e `server` em um arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="27ece-282">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="27ece-283">Proteger o Nginx de clickjacking</span><span class="sxs-lookup"><span data-stu-id="27ece-283">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="27ece-284">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), também conhecido como um *ataque por inferência na interface do usuário*, é um ataque mal-intencionado em que o visitante do site é levado a clicar em um link ou botão em uma página diferente daquela que está visitando atualmente.</span><span class="sxs-lookup"><span data-stu-id="27ece-284">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="27ece-285">Use `X-FRAME-OPTIONS` para proteger o site.</span><span class="sxs-lookup"><span data-stu-id="27ece-285">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="27ece-286">Para atenuar ataques de clickjacking:</span><span class="sxs-lookup"><span data-stu-id="27ece-286">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="27ece-287">Edite o arquivo *nginx.conf*:</span><span class="sxs-lookup"><span data-stu-id="27ece-287">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="27ece-288">Adicione a linha `add_header X-Frame-Options "SAMEORIGIN";`.</span><span class="sxs-lookup"><span data-stu-id="27ece-288">Add the line `add_header X-Frame-Options "SAMEORIGIN";`.</span></span>
1. <span data-ttu-id="27ece-289">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="27ece-289">Save the file.</span></span>
1. <span data-ttu-id="27ece-290">Reinicie o Nginx.</span><span class="sxs-lookup"><span data-stu-id="27ece-290">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="27ece-291">Detecção de tipo MIME</span><span class="sxs-lookup"><span data-stu-id="27ece-291">MIME-type sniffing</span></span>

<span data-ttu-id="27ece-292">Esse cabeçalho evita que a maioria dos navegadores faça detecção MIME de uma resposta distante do tipo de conteúdo declarado, visto que o cabeçalho instrui o navegador para não substituir o tipo de conteúdo de resposta.</span><span class="sxs-lookup"><span data-stu-id="27ece-292">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="27ece-293">Com a opção `nosniff`, se o servidor informa que o conteúdo é "text/html", o navegador renderiza-a como "text/html".</span><span class="sxs-lookup"><span data-stu-id="27ece-293">With the `nosniff` option, if the server says the content is "text/html", the browser renders it as "text/html".</span></span>

<span data-ttu-id="27ece-294">Edite o arquivo *nginx.conf*:</span><span class="sxs-lookup"><span data-stu-id="27ece-294">Edit the *nginx.conf* file:</span></span>

```bash
sudo nano /etc/nginx/nginx.conf
```

<span data-ttu-id="27ece-295">Adicione a linha `add_header X-Content-Type-Options "nosniff";` e salve o arquivo, depois reinicie o Nginx.</span><span class="sxs-lookup"><span data-stu-id="27ece-295">Add the line `add_header X-Content-Type-Options "nosniff";` and save the file, then restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="27ece-296">Sugestões de Nginx adicionais</span><span class="sxs-lookup"><span data-stu-id="27ece-296">Additional Nginx suggestions</span></span>

<span data-ttu-id="27ece-297">Depois de atualizar a estrutura compartilhada no servidor, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="27ece-297">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="27ece-298">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="27ece-298">Additional resources</span></span>

* [<span data-ttu-id="27ece-299">Pré-requisitos para o .NET Core no Linux</span><span class="sxs-lookup"><span data-stu-id="27ece-299">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="27ece-300">Nginx: versões binárias: pacotes Debian/Ubuntu oficiais</span><span class="sxs-lookup"><span data-stu-id="27ece-300">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="27ece-301">NGINX: usando o cabeçalho Encaminhado</span><span class="sxs-lookup"><span data-stu-id="27ece-301">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
