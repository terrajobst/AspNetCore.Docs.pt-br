---
title: Hospedar o ASP.NET Core no Linux com o Apache
author: guardrex
description: Saiba como configurar o Apache como um servidor proxy reverso no CentOS para redirecionar o tráfego HTTP para um aplicativo do Web ASP.NET Core em execução no Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 12/02/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 730ed1847ec5728657d56db3ccf0f1f5fab6b5dd
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717358"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="253db-103">Hospedar o ASP.NET Core no Linux com o Apache</span><span class="sxs-lookup"><span data-stu-id="253db-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="253db-104">Por [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="253db-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="253db-105">Usando este guia, saiba como configurar o [Apache](https://httpd.apache.org/) como um servidor proxy reverso no [CentOS 7](https://www.centos.org/) para redirecionar o tráfego HTTP para um aplicativo Web ASP.NET Core em execução no servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="253db-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="253db-106">A [extensão mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) e os módulos relacionados criam o proxy reverso do servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="253db-107">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="253db-107">Prerequisites</span></span>

* <span data-ttu-id="253db-108">Servidor que executa o CentOS 7 com uma conta de usuário padrão com privilégio sudo.</span><span class="sxs-lookup"><span data-stu-id="253db-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="253db-109">Instale o runtime do .NET Core no servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="253db-110">Visite a [página baixar o .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="253db-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="253db-111">Selecione a versão mais recente do .NET Core sem visualização.</span><span class="sxs-lookup"><span data-stu-id="253db-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="253db-112">Baixe o tempo de execução de não visualização mais recente na tabela em **executar aplicativos-tempo de execução**.</span><span class="sxs-lookup"><span data-stu-id="253db-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="253db-113">Selecione o link de **instruções do Gerenciador de pacotes** do Linux e siga as instruções do CentOS.</span><span class="sxs-lookup"><span data-stu-id="253db-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="253db-114">Um aplicativo ASP.NET Core existente.</span><span class="sxs-lookup"><span data-stu-id="253db-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="253db-115">A qualquer momento no futuro depois de atualizar a estrutura compartilhada, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="253db-116">Publicar e copiar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="253db-116">Publish and copy over the app</span></span>

<span data-ttu-id="253db-117">Configurar o aplicativo para um [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="253db-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="253db-118">Se o aplicativo for executado localmente e não estiver configurado para fazer conexões seguras (HTTPS), adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="253db-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="253db-119">Configure o aplicativo para lidar com conexões seguras locais.</span><span class="sxs-lookup"><span data-stu-id="253db-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="253db-120">Para obter mais informações, veja a seção [Configuração de HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="253db-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="253db-121">Remova `https://localhost:5001` (se houver) da propriedade `applicationUrl` no arquivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="253db-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="253db-122">Execute [dotnet publish](/dotnet/core/tools/dotnet-publish) do ambiente de desenvolvimento para empacotar um aplicativo em um diretório (por exemplo, *bin/Release/&lt;target_framework_moniker&gt;/publish*) que pode ser executado no servidor:</span><span class="sxs-lookup"><span data-stu-id="253db-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="253db-123">O aplicativo também poderá ser publicado como uma [implantação autossuficiente](/dotnet/core/deploying/#self-contained-deployments-scd) se você preferir não manter o runtime do .NET Core no servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="253db-124">Copie o aplicativo ASP.NET Core para o servidor usando uma ferramenta que se integre ao fluxo de trabalho da organização (por exemplo, SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="253db-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="253db-125">É comum para localizar os aplicativos Web no diretório *var* (por exemplo, *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="253db-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="253db-126">Em um cenário de implantação de produção, um fluxo de trabalho de integração contínua faz o trabalho de publicar o aplicativo e copiar os ativos para o servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="253db-127">Configurar um servidor proxy</span><span class="sxs-lookup"><span data-stu-id="253db-127">Configure a proxy server</span></span>

<span data-ttu-id="253db-128">Um proxy reverso é uma configuração comum para atender a aplicativos Web dinâmicos.</span><span class="sxs-lookup"><span data-stu-id="253db-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="253db-129">O proxy reverso encerra a solicitação HTTP e a encaminha para o aplicativo ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="253db-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="253db-130">Um servidor proxy é aquele que encaminha as solicitações de cliente para outro servidor, em vez de atendê-las por conta própria.</span><span class="sxs-lookup"><span data-stu-id="253db-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="253db-131">Um proxy reverso encaminha para um destino fixo, normalmente, em nome de clientes arbitrários.</span><span class="sxs-lookup"><span data-stu-id="253db-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="253db-132">Neste guia, o Apache é configurado como o proxy reverso em execução no mesmo servidor em que o Kestrel está servindo o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="253db-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="253db-133">Uma vez que as solicitações são encaminhadas pelo proxy reverso, use o [Middleware de Cabeçalhos Encaminhados](xref:host-and-deploy/proxy-load-balancer) do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/).</span><span class="sxs-lookup"><span data-stu-id="253db-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="253db-134">O middleware atualiza o `Request.Scheme` usando o cabeçalho `X-Forwarded-Proto`, de forma que URIs de redirecionamento e outras políticas de segurança funcionam corretamente.</span><span class="sxs-lookup"><span data-stu-id="253db-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="253db-135">Qualquer componente que dependa do esquema, como autenticação, geração de link, redirecionamentos e localização geográfica, deverá ser colocado depois de invocar o Middleware de Cabeçalhos Encaminhados.</span><span class="sxs-lookup"><span data-stu-id="253db-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="253db-136">Como regra geral, o Middleware de Cabeçalhos Encaminhados deve ser executado antes de outro middleware, exceto middleware de tratamento de erro e de diagnóstico.</span><span class="sxs-lookup"><span data-stu-id="253db-136">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="253db-137">Essa ordenação garantirá que o middleware conte com informações de cabeçalhos encaminhadas que podem consumir os valores de cabeçalho para processamento.</span><span class="sxs-lookup"><span data-stu-id="253db-137">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="253db-138">Invoque o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure` antes de chamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ou outro middleware de esquema de autenticação semelhante.</span><span class="sxs-lookup"><span data-stu-id="253db-138">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="253db-139">Configure o middleware para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="253db-139">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="253db-140">Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, os cabeçalhos padrão para encaminhar serão `None`.</span><span class="sxs-lookup"><span data-stu-id="253db-140">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="253db-141">Os proxies em execução em endereços de loopback (127.0.0.0/8, [::1]), incluindo o endereço de host local padrão (127.0.0.1), são confiáveis por padrão.</span><span class="sxs-lookup"><span data-stu-id="253db-141">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="253db-142">Se outros proxies ou redes confiáveis em que a organização trata solicitações entre a Internet e o servidor Web, adicione-os à lista de <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> ou <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="253db-142">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="253db-143">O exemplo a seguir adiciona um servidor proxy confiável no endereço IP 10.0.0.100 ao Middleware de cabeçalhos encaminhados `KnownProxies` em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="253db-143">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="253db-144">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="253db-144">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="253db-145">Instalar o Apache</span><span class="sxs-lookup"><span data-stu-id="253db-145">Install Apache</span></span>

<span data-ttu-id="253db-146">Atualize os pacotes CentOS para as respectivas versões estáveis mais recentes:</span><span class="sxs-lookup"><span data-stu-id="253db-146">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="253db-147">Instale o servidor Web do Apache no CentOS com um único comando `yum`:</span><span class="sxs-lookup"><span data-stu-id="253db-147">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="253db-148">Saída de exemplo depois de executar o comando:</span><span class="sxs-lookup"><span data-stu-id="253db-148">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="253db-149">Neste exemplo, o resultado reflete httpd.86_64, pois a versão do CentOS 7 é de 64 bits.</span><span class="sxs-lookup"><span data-stu-id="253db-149">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="253db-150">Para verificar o local em que o Apache está instalado, execute `whereis httpd` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="253db-150">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="253db-151">Configurar o Apache</span><span class="sxs-lookup"><span data-stu-id="253db-151">Configure Apache</span></span>

<span data-ttu-id="253db-152">Os arquivos de configuração do Apache estão localizados no diretório `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="253db-152">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="253db-153">Qualquer arquivo com a extensão *.conf* é processado em ordem alfabética, além dos arquivos de configuração do módulo em `/etc/httpd/conf.modules.d/`, que contém todos os arquivos de configuração necessários para carregar os módulos.</span><span class="sxs-lookup"><span data-stu-id="253db-153">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="253db-154">Crie um arquivo de configuração chamado *helloapp.conf* para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="253db-154">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="253db-155">O bloco `VirtualHost` pode aparecer várias vezes, em um ou mais arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-155">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="253db-156">No arquivo de configuração anterior, o Apache aceita tráfego público na porta 80.</span><span class="sxs-lookup"><span data-stu-id="253db-156">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="253db-157">O domínio `www.example.com` está sendo atendido e o alias `*.example.com` é resolvido para o mesmo site.</span><span class="sxs-lookup"><span data-stu-id="253db-157">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="253db-158">Veja [Suporte a host virtual baseado em nome](https://httpd.apache.org/docs/current/vhosts/name-based.html) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="253db-158">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="253db-159">As solicitações passadas por proxy na raiz para a porta 5000 do servidor em 127.0.0.1.</span><span class="sxs-lookup"><span data-stu-id="253db-159">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="253db-160">Para a comunicação bidirecional, `ProxyPass` e `ProxyPassReverse` são necessários.</span><span class="sxs-lookup"><span data-stu-id="253db-160">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="253db-161">Para alterar o IP/porta do Kestrel, veja [Kestrel: configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="253db-161">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="253db-162">Falha ao especificar uma [diretiva ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) no bloco **VirtualHost** expõe seu aplicativo para vulnerabilidades de segurança.</span><span class="sxs-lookup"><span data-stu-id="253db-162">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="253db-163">Associações de curinga de subdomínio (por exemplo, `*.example.com`) não oferecerão esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="253db-163">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="253db-164">Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="253db-164">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="253db-165">O registro em log pode ser configurado por `VirtualHost` usando diretivas `ErrorLog` e `CustomLog`.</span><span class="sxs-lookup"><span data-stu-id="253db-165">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="253db-166">`ErrorLog` é o local em que o servidor registrará em log os erros, enquanto `CustomLog` define o nome de arquivo e o formato do arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="253db-166">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="253db-167">Nesse caso, esse é o local em que as informações de solicitação são registradas em log.</span><span class="sxs-lookup"><span data-stu-id="253db-167">In this case, this is where request information is logged.</span></span> <span data-ttu-id="253db-168">Há uma linha para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="253db-168">There's one line for each request.</span></span>

<span data-ttu-id="253db-169">Salve o arquivo e teste a configuração.</span><span class="sxs-lookup"><span data-stu-id="253db-169">Save the file and test the configuration.</span></span> <span data-ttu-id="253db-170">Se tudo passar, a resposta deverá ser `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="253db-170">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="253db-171">Reinicie o Apache:</span><span class="sxs-lookup"><span data-stu-id="253db-171">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="253db-172">Monitorar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="253db-172">Monitor the app</span></span>

<span data-ttu-id="253db-173">O Apache agora está configurado para encaminhar solicitações feitas a `http://localhost:80` para o aplicativo ASP.NET Core em execução no Kestrel em `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="253db-173">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="253db-174">No entanto, o Apache não está configurado para gerenciar o processo do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="253db-174">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="253db-175">Use *systemd* e crie um arquivo de serviço para iniciar e monitorar o aplicativo Web subjacente.</span><span class="sxs-lookup"><span data-stu-id="253db-175">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="253db-176">*systemd* é um sistema de inicialização que fornece muitos recursos poderosos para iniciar, parar e gerenciar processos.</span><span class="sxs-lookup"><span data-stu-id="253db-176">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="253db-177">Criar o arquivo de serviço</span><span class="sxs-lookup"><span data-stu-id="253db-177">Create the service file</span></span>

<span data-ttu-id="253db-178">Crie o arquivo de definição de serviço:</span><span class="sxs-lookup"><span data-stu-id="253db-178">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="253db-179">Eis um exemplo de arquivo de serviço para o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="253db-179">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="253db-180">Se o usuário *apache* não for usado pela configuração, o usuário precisará ser criado primeiro e a propriedade adequada dos arquivos precisará ser concedida a ele.</span><span class="sxs-lookup"><span data-stu-id="253db-180">If the user *apache* isn't used by the configuration, the user must be created first and given proper ownership of files.</span></span>

<span data-ttu-id="253db-181">Use `TimeoutStopSec` para configurar a duração do tempo de espera para o aplicativo desligar depois de receber o sinal de interrupção inicial.</span><span class="sxs-lookup"><span data-stu-id="253db-181">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="253db-182">Se o aplicativo não desligar nesse período, o SIGKILL será emitido para encerrá-lo.</span><span class="sxs-lookup"><span data-stu-id="253db-182">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="253db-183">Forneça o valor como segundos sem unidade (por exemplo, `150`), um valor de duração (por exemplo, `2min 30s`) ou `infinity` para desabilitar o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="253db-183">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="253db-184">`TimeoutStopSec` é revertido para o valor padrão de `DefaultTimeoutStopSec` no arquivo de configuração do gerenciador (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf* e *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="253db-184">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="253db-185">O tempo limite padrão para a maioria das distribuições é de 90 segundos.</span><span class="sxs-lookup"><span data-stu-id="253db-185">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="253db-186">Alguns valores (por exemplo, cadeias de conexão de SQL) devem ser escapadas para que os provedores de configuração leiam as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="253db-186">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="253db-187">Use o seguinte comando para gerar um valor corretamente com caracteres de escape para uso no arquivo de configuração:</span><span class="sxs-lookup"><span data-stu-id="253db-187">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="253db-188">Separadores do tipo dois-pontos (`:`) não são compatíveis com nomes de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="253db-188">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="253db-189">Use um sublinhado duplo (`__`) no lugar de dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="253db-189">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="253db-190">O [provedor de configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converte caracteres de sublinhado duplo em dois-pontos quando as variáveis de ambiente são lidas na configuração.</span><span class="sxs-lookup"><span data-stu-id="253db-190">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="253db-191">No exemplo a seguir, a chave de cadeia de conexão `ConnectionStrings:DefaultConnection` está definida no arquivo de definição de serviço como `ConnectionStrings__DefaultConnection`:</span><span class="sxs-lookup"><span data-stu-id="253db-191">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="253db-192">Salve o arquivo e habilite o serviço:</span><span class="sxs-lookup"><span data-stu-id="253db-192">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="253db-193">Inicie o serviço e verifique se ele está em execução:</span><span class="sxs-lookup"><span data-stu-id="253db-193">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="253db-194">Com o proxy reverso configurado e o Kestrel gerenciado por meio de *systemd*, o aplicativo Web está totalmente configurado e pode ser acessado em um navegador no computador local em `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="253db-194">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="253db-195">Inspecionando os cabeçalhos de resposta, o cabeçalho **Server** indica que o aplicativo ASP.NET Core é servido pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="253db-195">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="253db-196">Exibir logs</span><span class="sxs-lookup"><span data-stu-id="253db-196">View logs</span></span>

<span data-ttu-id="253db-197">Já que o aplicativo Web usando Kestrel é gerenciado usando *systemd*, os eventos e os processos são registrados em um diário centralizado.</span><span class="sxs-lookup"><span data-stu-id="253db-197">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="253db-198">No entanto, esse diário contém todas as entradas para todos os serviços e processos gerenciados por *systemd*.</span><span class="sxs-lookup"><span data-stu-id="253db-198">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="253db-199">Para exibir os itens específicos de `kestrel-helloapp.service`, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="253db-199">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="253db-200">Para filtragem de hora, especifique opções de tempo com o comando.</span><span class="sxs-lookup"><span data-stu-id="253db-200">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="253db-201">Por exemplo, use `--since today` para filtrar o dia atual ou `--until 1 hour ago` para ver as entradas da hora anterior.</span><span class="sxs-lookup"><span data-stu-id="253db-201">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="253db-202">Para obter mais informações, confira a [página de manual para journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="253db-202">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="253db-203">Proteção de dados</span><span class="sxs-lookup"><span data-stu-id="253db-203">Data protection</span></span>

<span data-ttu-id="253db-204">A [pilha de proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) do ASP.NET Core, incluindo o middleware de autenticação (por exemplo, o middleware de cookie) e as proteções de CSRF (solicitação intersite forjada).</span><span class="sxs-lookup"><span data-stu-id="253db-204">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="253db-205">Mesmo se as APIs de Proteção de Dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente.</span><span class="sxs-lookup"><span data-stu-id="253db-205">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="253db-206">Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="253db-206">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="253db-207">Se o token de autenticação for armazenado na memória quando o aplicativo for reiniciado:</span><span class="sxs-lookup"><span data-stu-id="253db-207">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="253db-208">Todos os tokens de autenticação baseados em cookies serão invalidados.</span><span class="sxs-lookup"><span data-stu-id="253db-208">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="253db-209">Os usuários precisam entrar novamente na próxima solicitação deles.</span><span class="sxs-lookup"><span data-stu-id="253db-209">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="253db-210">Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados.</span><span class="sxs-lookup"><span data-stu-id="253db-210">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="253db-211">Isso pode incluir os [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [cookies TempData do MVC do ASP.NET Core](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="253db-211">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="253db-212">Para configurar a proteção de dados de modo que ela mantenha e criptografe o token de autenticação, consulte:</span><span class="sxs-lookup"><span data-stu-id="253db-212">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="253db-213">Proteger o aplicativo</span><span class="sxs-lookup"><span data-stu-id="253db-213">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="253db-214">Configurar o firewall</span><span class="sxs-lookup"><span data-stu-id="253db-214">Configure firewall</span></span>

<span data-ttu-id="253db-215">*Firewalld* é um daemon dinâmico para gerenciar o firewall com suporte para zonas de rede.</span><span class="sxs-lookup"><span data-stu-id="253db-215">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="253db-216">Portas e filtragem de pacote ainda podem ser gerenciados pelo iptables.</span><span class="sxs-lookup"><span data-stu-id="253db-216">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="253db-217">*Firewalld* deve ser instalado por padrão.</span><span class="sxs-lookup"><span data-stu-id="253db-217">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="253db-218">`yum` pode ser usado para instalar o pacote ou verificar se ele está instalado.</span><span class="sxs-lookup"><span data-stu-id="253db-218">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="253db-219">Use `firewalld` para abrir somente as portas necessárias para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="253db-219">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="253db-220">Nesse caso, as portas 80 e 443 são usadas.</span><span class="sxs-lookup"><span data-stu-id="253db-220">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="253db-221">Os comandos a seguir definem permanentemente as portas 80 e 443 para estarem abertas:</span><span class="sxs-lookup"><span data-stu-id="253db-221">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="253db-222">Recarregue as configurações de firewall.</span><span class="sxs-lookup"><span data-stu-id="253db-222">Reload the firewall settings.</span></span> <span data-ttu-id="253db-223">Verifique os serviços e as portas disponíveis na zona padrão.</span><span class="sxs-lookup"><span data-stu-id="253db-223">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="253db-224">As opções estão disponíveis por meio da inspeção de `firewall-cmd -h`.</span><span class="sxs-lookup"><span data-stu-id="253db-224">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="253db-225">Configuração de HTTPS</span><span class="sxs-lookup"><span data-stu-id="253db-225">HTTPS configuration</span></span>

<span data-ttu-id="253db-226">**Configurar o aplicativo para conexões seguras (HTTPS) locais**</span><span class="sxs-lookup"><span data-stu-id="253db-226">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="253db-227">O comando [dotnet run](/dotnet/core/tools/dotnet-run) usa o arquivo *Properties/launchSettings.json* do aplicativo, que configura o aplicativo para escutar nas URLs fornecidas pela propriedade `applicationUrl` (por exemplo, `https://localhost:5001; http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="253db-227">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="253db-228">Configure o aplicativo para usar um certificado no desenvolvimento para o comando `dotnet run` ou no ambiente de desenvolvimento (F5 ou Ctrl + F5 no Visual Studio Code) usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="253db-228">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="253db-229">[Substituir o certificado padrão da configuração](xref:fundamentals/servers/kestrel#configuration) (*Recomendado*)</span><span class="sxs-lookup"><span data-stu-id="253db-229">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="253db-230">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="253db-230">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="253db-231">**Configurar o proxy reverso para conexões de cliente seguras (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="253db-231">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="253db-232">Para configurar o Apache para HTTPS, o módulo *mod_ssl* é usado.</span><span class="sxs-lookup"><span data-stu-id="253db-232">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="253db-233">Quando o módulo *httpd* foi instalado, o módulo *mod_ssl* também foi instalado.</span><span class="sxs-lookup"><span data-stu-id="253db-233">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="253db-234">Se ele não foi instalado, use `yum` para adicioná-lo à configuração.</span><span class="sxs-lookup"><span data-stu-id="253db-234">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="253db-235">Para impor o HTTPS, instale o módulo `mod_rewrite` para habilitar a regravação de URL:</span><span class="sxs-lookup"><span data-stu-id="253db-235">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="253db-236">Modifique o arquivo *helloapp.conf* para habilitar a regravação de URL e proteger a comunicação na porta 443:</span><span class="sxs-lookup"><span data-stu-id="253db-236">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="253db-237">Este exemplo usa um certificado gerado localmente.</span><span class="sxs-lookup"><span data-stu-id="253db-237">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="253db-238">**SSLCertificateFile** deve ser o arquivo de certificado primário para o nome de domínio.</span><span class="sxs-lookup"><span data-stu-id="253db-238">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="253db-239">**SSLCertificateKeyFile** deve ser o arquivo de chave gerado quando o CSR é criado.</span><span class="sxs-lookup"><span data-stu-id="253db-239">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="253db-240">**SSLCertificateChainFile** deve ser o arquivo de certificado intermediário (se houver) fornecido pela autoridade de certificação.</span><span class="sxs-lookup"><span data-stu-id="253db-240">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="253db-241">Salve o arquivo e teste a configuração:</span><span class="sxs-lookup"><span data-stu-id="253db-241">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="253db-242">Reinicie o Apache:</span><span class="sxs-lookup"><span data-stu-id="253db-242">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="253db-243">Sugestões adicionais do Apache</span><span class="sxs-lookup"><span data-stu-id="253db-243">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="253db-244">Reiniciar aplicativos com atualizações de estrutura compartilhadas</span><span class="sxs-lookup"><span data-stu-id="253db-244">Restart apps with shared framework updates</span></span>

<span data-ttu-id="253db-245">Depois de atualizar a estrutura compartilhada no servidor, reinicie o ASP.NET Core aplicativos hospedados pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-245">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="253db-246">Cabeçalhos adicionais</span><span class="sxs-lookup"><span data-stu-id="253db-246">Additional headers</span></span>

<span data-ttu-id="253db-247">Para proteção contra ataques mal-intencionados, há alguns cabeçalhos que devem ser modificados ou adicionados.</span><span class="sxs-lookup"><span data-stu-id="253db-247">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="253db-248">Verifique se o módulo `mod_headers` está instalado:</span><span class="sxs-lookup"><span data-stu-id="253db-248">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="253db-249">Proteger o Apache contra ataques de clickjacking</span><span class="sxs-lookup"><span data-stu-id="253db-249">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="253db-250">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), também conhecido como um *ataque por inferência na interface do usuário*, é um ataque mal-intencionado em que o visitante do site é levado a clicar em um link ou botão em uma página diferente daquela que está visitando atualmente.</span><span class="sxs-lookup"><span data-stu-id="253db-250">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="253db-251">Use `X-FRAME-OPTIONS` para proteger o site.</span><span class="sxs-lookup"><span data-stu-id="253db-251">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="253db-252">Para atenuar ataques de clickjacking:</span><span class="sxs-lookup"><span data-stu-id="253db-252">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="253db-253">Edite o arquivo *httpd.conf*:</span><span class="sxs-lookup"><span data-stu-id="253db-253">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="253db-254">Adicione a linha `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span><span class="sxs-lookup"><span data-stu-id="253db-254">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="253db-255">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="253db-255">Save the file.</span></span>
1. <span data-ttu-id="253db-256">Reinicie o Apache.</span><span class="sxs-lookup"><span data-stu-id="253db-256">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="253db-257">Detecção de tipo MIME</span><span class="sxs-lookup"><span data-stu-id="253db-257">MIME-type sniffing</span></span>

<span data-ttu-id="253db-258">O cabeçalho `X-Content-Type-Options` impedirá o Internet Explorer de *farejar por MIME* (determinar o `Content-Type` de um arquivo com base no conteúdo do arquivo).</span><span class="sxs-lookup"><span data-stu-id="253db-258">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="253db-259">Se o servidor define o cabeçalho `Content-Type` para `text/html` com a opção `nosniff` definida, o Internet Explorer renderiza o conteúdo como `text/html`, independentemente do conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="253db-259">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="253db-260">Edite o arquivo *httpd.conf*:</span><span class="sxs-lookup"><span data-stu-id="253db-260">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="253db-261">Adicione a linha `Header set X-Content-Type-Options "nosniff"`.</span><span class="sxs-lookup"><span data-stu-id="253db-261">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="253db-262">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="253db-262">Save the file.</span></span> <span data-ttu-id="253db-263">Reinicie o Apache.</span><span class="sxs-lookup"><span data-stu-id="253db-263">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="253db-264">Balanceamento de carga</span><span class="sxs-lookup"><span data-stu-id="253db-264">Load Balancing</span></span>

<span data-ttu-id="253db-265">Este exemplo mostra como instalar e configurar o Apache no CentOS 7 e no Kestrel no mesmo computador da instância.</span><span class="sxs-lookup"><span data-stu-id="253db-265">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="253db-266">Para não ter um ponto único de falha, o uso de *mod_proxy_balancer* e a modificação do **VirtualHost** permitiriam o gerenciamento de várias instâncias dos aplicativos Web protegidos pelo servidor proxy do Apache.</span><span class="sxs-lookup"><span data-stu-id="253db-266">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="253db-267">No arquivo de configuração mostrado abaixo, uma instância adicional do `helloapp` é configurada para ser executada na porta 5001.</span><span class="sxs-lookup"><span data-stu-id="253db-267">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="253db-268">A seção *Proxy* é definida com uma configuração de balanceador com dois membros para balancear carga de *byrequests*.</span><span class="sxs-lookup"><span data-stu-id="253db-268">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="253db-269">Limites de taxa</span><span class="sxs-lookup"><span data-stu-id="253db-269">Rate Limits</span></span>

<span data-ttu-id="253db-270">Usando *mod_ratelimit*, que está incluído no módulo *httpd*, a largura de banda de clientes pode ser limitada:</span><span class="sxs-lookup"><span data-stu-id="253db-270">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="253db-271">O arquivo de exemplo limita a largura de banda a 600 KB/s no local raiz:</span><span class="sxs-lookup"><span data-stu-id="253db-271">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="253db-272">Campos de cabeçalho da solicitação muito grandes</span><span class="sxs-lookup"><span data-stu-id="253db-272">Long request header fields</span></span>

<span data-ttu-id="253db-273">As configurações padrão do servidor proxy normalmente limitam os campos de cabeçalho de solicitação a 8.190 bytes.</span><span class="sxs-lookup"><span data-stu-id="253db-273">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="253db-274">Um aplicativo pode exigir campos maiores do que o padrão (por exemplo, aplicativos que usam [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="253db-274">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="253db-275">Se forem necessários campos mais longos, a diretiva [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) do servidor proxy exigirá ajuste.</span><span class="sxs-lookup"><span data-stu-id="253db-275">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="253db-276">O valor a ser aplicado depende do cenário.</span><span class="sxs-lookup"><span data-stu-id="253db-276">The value to apply depends on the scenario.</span></span> <span data-ttu-id="253db-277">Para obter mais informações, confira a documentação do servidor.</span><span class="sxs-lookup"><span data-stu-id="253db-277">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="253db-278">Não aumente o valor padrão de `LimitRequestFieldSize` a menos que necessário.</span><span class="sxs-lookup"><span data-stu-id="253db-278">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="253db-279">Aumentar esse valor aumenta o risco de estouro de buffer (estouro) e ataques de DoS (negação de serviço) por usuários mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="253db-279">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="253db-280">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="253db-280">Additional resources</span></span>

* [<span data-ttu-id="253db-281">Pré-requisitos para o .NET Core no Linux</span><span class="sxs-lookup"><span data-stu-id="253db-281">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
