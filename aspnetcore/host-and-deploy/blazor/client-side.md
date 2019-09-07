---
title: Hospedar e implantar o ASP.NET Core Blazor no lado do cliente
author: guardrex
description: Veja como hospedar e implantar um aplicativo do Blazor usando o ASP.NET Core, a CDN (Rede de Distribuição de Conteúdo), servidores de arquivos e páginas do GitHub.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: c9822205d38f765cf80748bc2b379c11ec7c1c57
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773600"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a><span data-ttu-id="765f2-103">Hospedar e implantar o ASP.NET Core Blazor no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="765f2-103">Host and deploy ASP.NET Core Blazor client-side</span></span>

<span data-ttu-id="765f2-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="765f2-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="765f2-105">Com o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side):</span><span class="sxs-lookup"><span data-stu-id="765f2-105">With the [client-side hosting model](xref:blazor/hosting-models#client-side):</span></span>

* <span data-ttu-id="765f2-106">O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="765f2-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="765f2-107">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="765f2-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="765f2-108">Há suporte para as seguintes estratégias de implantação:</span><span class="sxs-lookup"><span data-stu-id="765f2-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="765f2-109">O aplicativo do Blazor é atendido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="765f2-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="765f2-110">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="765f2-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="765f2-111">O aplicativo do Blazor é colocado em um serviço ou um servidor Web de hospedagem estático, em que o .NET não é usado para atender ao aplicativo do Blazor.</span><span class="sxs-lookup"><span data-stu-id="765f2-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="765f2-112">Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um aplicativo de alto nível do cliente como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="765f2-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor client-side app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="765f2-113">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="765f2-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="765f2-114">O roteamento de solicitações para componentes de página em um aplicativo do lado do cliente não é tão simples quanto o roteamento de solicitações em um aplicativo hospedado no lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="765f2-114">Routing requests for page components in a client-side app isn't as straightforward as routing requests in a server-side, hosted app.</span></span> <span data-ttu-id="765f2-115">Considere um aplicativo do lado do cliente com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="765f2-115">Consider a client-side app with two components:</span></span>

* <span data-ttu-id="765f2-116">*Main.razor* &ndash; É carregado na raiz do aplicativo e contém um link para o componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="765f2-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="765f2-117">*About.Razor* &ndash; componente `About`.</span><span class="sxs-lookup"><span data-stu-id="765f2-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="765f2-118">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="765f2-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="765f2-119">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="765f2-119">The browser makes a request.</span></span>
1. <span data-ttu-id="765f2-120">A página padrão é retornada, que é geralmente é *index.html*.</span><span class="sxs-lookup"><span data-stu-id="765f2-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="765f2-121">A *index.html* inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765f2-121">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="765f2-122">O roteador do Blazor é carregado e o componente `Main` Razor é renderizado.</span><span class="sxs-lookup"><span data-stu-id="765f2-122">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="765f2-123">Na página principal, a seleção do link para o componente `About` funciona no cliente porque o roteador do Blazor impede que o navegador faça uma solicitação na Internet para `www.contoso.com` de `About` e atende ao componente `About` renderizado.</span><span class="sxs-lookup"><span data-stu-id="765f2-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="765f2-124">Todas as solicitações de pontos de extremidades internos *no aplicativo do lado do cliente* funcionam da mesma maneira: Não são disparadas solicitações baseadas em navegador para os recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="765f2-124">All of the requests for internal endpoints *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="765f2-125">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="765f2-125">The router handles the requests internally.</span></span>

<span data-ttu-id="765f2-126">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="765f2-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="765f2-127">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="765f2-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="765f2-128">Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="765f2-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="765f2-129">Quando a *index.html* for retornada, o roteador do lado do cliente do aplicativo assumirá o controle e responderá com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="765f2-129">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="765f2-130">Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo *Web. config* publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765f2-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="765f2-131">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="765f2-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="765f2-132">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="765f2-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="765f2-133">Uma *implantação hospedada* atende ao aplicativo do lado do cliente do Blazor para navegadores de um [aplicativo do ASP.NET Core](xref:index) que é executado em um servidor da web.</span><span class="sxs-lookup"><span data-stu-id="765f2-133">A *hosted deployment* serves the Blazor client-side app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="765f2-134">O aplicativo do Blazor é incluído com o aplicativo do ASP.NET Core na saída publicada para que ambos sejam implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="765f2-134">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="765f2-135">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="765f2-135">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="765f2-136">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto **Blazor WebAssembly App** (modelo `blazorwasm` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)) com a opção **Hospedado** selecionada.</span><span class="sxs-lookup"><span data-stu-id="765f2-136">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected.</span></span>

<span data-ttu-id="765f2-137">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="765f2-137">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="765f2-138">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="765f2-138">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="765f2-139">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="765f2-139">Standalone deployment</span></span>

<span data-ttu-id="765f2-140">Uma *implantação autônoma* atende ao aplicativo do lado do cliente do Blazor como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="765f2-140">A *standalone deployment* serves the Blazor client-side app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="765f2-141">Qualquer servidor de arquivos estático é capaz de atender ao aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="765f2-141">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="765f2-142">Ativos de implantação autônomos são publicados na pasta */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist*.</span><span class="sxs-lookup"><span data-stu-id="765f2-142">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="765f2-143">IIS</span><span class="sxs-lookup"><span data-stu-id="765f2-143">IIS</span></span>

<span data-ttu-id="765f2-144">O IIS é um servidor de arquivos estático com capacidade para aplicativos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="765f2-144">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="765f2-145">Para configurar o IIS para hospedar o Blazor, confira [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Criar um site estático no IIS).</span><span class="sxs-lookup"><span data-stu-id="765f2-145">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="765f2-146">Os ativos publicados são criados na pasta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="765f2-146">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="765f2-147">Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="765f2-147">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="765f2-148">web.config</span><span class="sxs-lookup"><span data-stu-id="765f2-148">web.config</span></span>

<span data-ttu-id="765f2-149">Quando um projeto Blazor é publicado, um arquivo *web.config* é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="765f2-149">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="765f2-150">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="765f2-150">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="765f2-151">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="765f2-151">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="765f2-152">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="765f2-152">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="765f2-153">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="765f2-153">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="765f2-154">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="765f2-154">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="765f2-155">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="765f2-155">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="765f2-156">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="765f2-156">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="765f2-157">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="765f2-157">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="765f2-158">Atender ao subdiretório em que residem os ativos estáticos do aplicativo ( *{ASSEMBLY NAME}/dist/{PATH REQUESTED}* ).</span><span class="sxs-lookup"><span data-stu-id="765f2-158">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="765f2-159">Criar o roteamento de fallback do SPA, de modo que as solicitações de ativos, que não sejam arquivos, sejam redirecionadas ao documento padrão do aplicativo na pasta de ativos estáticos dele ( *{ASSEMBLY NAME}/dist/index.html*).</span><span class="sxs-lookup"><span data-stu-id="765f2-159">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="765f2-160">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="765f2-160">Install the URL Rewrite Module</span></span>

<span data-ttu-id="765f2-161">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="765f2-161">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="765f2-162">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="765f2-162">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="765f2-163">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="765f2-163">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="765f2-164">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="765f2-164">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="765f2-165">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="765f2-165">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="765f2-166">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="765f2-166">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="765f2-167">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="765f2-167">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="765f2-168">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="765f2-168">Copy the installer to the server.</span></span> <span data-ttu-id="765f2-169">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="765f2-169">Run the installer.</span></span> <span data-ttu-id="765f2-170">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="765f2-170">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="765f2-171">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="765f2-171">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="765f2-172">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="765f2-172">Configure the website</span></span>

<span data-ttu-id="765f2-173">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765f2-173">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="765f2-174">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="765f2-174">The folder contains:</span></span>

* <span data-ttu-id="765f2-175">O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.</span><span class="sxs-lookup"><span data-stu-id="765f2-175">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="765f2-176">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765f2-176">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="765f2-177">Hospedar como um subaplicativo do IIS</span><span class="sxs-lookup"><span data-stu-id="765f2-177">Host as an IIS sub-app</span></span>

<span data-ttu-id="765f2-178">Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="765f2-178">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="765f2-179">Desabilite o manipulador do módulo ASP.NET Core herdado.</span><span class="sxs-lookup"><span data-stu-id="765f2-179">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="765f2-180">Remova o manipulador do arquivo *Web. config* publicado de um aplicativo mais incrivelmente adicionando uma `<handlers>` seção ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="765f2-180">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="765f2-181">Desabilite a herança da seção `<system.webServer>` do aplicativo raiz (pai) usando um `<location>` elemento `inheritInChildApplications` com definido `false`como:</span><span class="sxs-lookup"><span data-stu-id="765f2-181">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="765f2-182">A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="765f2-182">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="765f2-183">Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="765f2-183">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="765f2-184">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="765f2-184">Troubleshooting</span></span>

<span data-ttu-id="765f2-185">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="765f2-185">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="765f2-186">Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="765f2-186">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="765f2-187">Isso impede que o Gerenciador do IIS carregue a configuração do site e que o site atenda aos arquivos estáticos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="765f2-187">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="765f2-188">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="765f2-188">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="765f2-189">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="765f2-189">Azure Storage</span></span>

<span data-ttu-id="765f2-190">A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos com mais de um servidor.</span><span class="sxs-lookup"><span data-stu-id="765f2-190">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="765f2-191">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="765f2-191">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="765f2-192">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="765f2-192">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="765f2-193">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="765f2-193">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="765f2-194">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="765f2-194">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="765f2-195">Os componentes do Razor e outros pontos de extremidade que não são arquivos não residem em caminhos físicos no conteúdo estático armazenado pelo serviço de blob.</span><span class="sxs-lookup"><span data-stu-id="765f2-195">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="765f2-196">Quando uma solicitação para um desses recursos é recebida, a qual deve passar pelo Blazor, o erro *404 - Não Encontrado* gerado pelo serviço de blob encaminha a solicitação para o **Caminho do documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="765f2-196">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="765f2-197">O blob *index.html* retorna, e o roteador do Blazor carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="765f2-197">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="765f2-198">Para saber mais, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="765f2-198">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="765f2-199">Nginx</span><span class="sxs-lookup"><span data-stu-id="765f2-199">Nginx</span></span>

<span data-ttu-id="765f2-200">O arquivo *nginx.conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *index.html* sempre que ele não puder encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="765f2-200">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="765f2-201">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="765f2-201">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="765f2-202">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="765f2-202">Nginx in Docker</span></span>

<span data-ttu-id="765f2-203">Para hospedar o Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem do Nginx baseada no Alpine.</span><span class="sxs-lookup"><span data-stu-id="765f2-203">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="765f2-204">Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.</span><span class="sxs-lookup"><span data-stu-id="765f2-204">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="765f2-205">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="765f2-205">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="765f2-206">Páginas do GitHub</span><span class="sxs-lookup"><span data-stu-id="765f2-206">GitHub Pages</span></span>

<span data-ttu-id="765f2-207">Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="765f2-207">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="765f2-208">Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="765f2-208">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="765f2-209">Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="765f2-209">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="765f2-210">Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*.</span><span class="sxs-lookup"><span data-stu-id="765f2-210">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="765f2-211">Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="765f2-211">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="765f2-212">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="765f2-212">Host configuration values</span></span>

<span data-ttu-id="765f2-213">Os aplicativos do Blazor que usam o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side) podem aceitar os seguintes valores de configuração do host como argumentos de linha de comando no tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="765f2-213">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="765f2-214">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="765f2-214">Content Root</span></span>

<span data-ttu-id="765f2-215">O argumento `--contentroot` define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765f2-215">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="765f2-216">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765f2-216">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="765f2-217">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="765f2-217">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="765f2-218">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="765f2-218">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="765f2-219">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="765f2-219">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="765f2-220">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="765f2-220">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="765f2-221">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="765f2-221">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="765f2-222">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="765f2-222">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="765f2-223">Caminho base</span><span class="sxs-lookup"><span data-stu-id="765f2-223">Path base</span></span>

<span data-ttu-id="765f2-224">O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz ( `<base>` a `href` marca é definida para um caminho diferente `/` de para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="765f2-224">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="765f2-225">Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="765f2-225">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="765f2-226">Para obter mais informações, consulte [caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="765f2-226">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="765f2-227">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="765f2-227">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="765f2-228">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="765f2-228">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="765f2-229">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="765f2-229">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="765f2-230">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="765f2-230">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="765f2-231">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="765f2-231">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="765f2-232">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="765f2-232">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="765f2-233">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="765f2-233">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="765f2-234">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="765f2-234">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="765f2-235">URLs</span><span class="sxs-lookup"><span data-stu-id="765f2-235">URLs</span></span>

<span data-ttu-id="765f2-236">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="765f2-236">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="765f2-237">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="765f2-237">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="765f2-238">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="765f2-238">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="765f2-239">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="765f2-239">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="765f2-240">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="765f2-240">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="765f2-241">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="765f2-241">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="765f2-242">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="765f2-242">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="765f2-243">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="765f2-243">Configure the Linker</span></span>

<span data-ttu-id="765f2-244">O Blazor executa a vinculação de IL (linguagem intermediária) em cada build para remover a IL desnecessária dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="765f2-244">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="765f2-245">A vinculação de assembly pode ser controlada no build.</span><span class="sxs-lookup"><span data-stu-id="765f2-245">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="765f2-246">Para obter mais informações, consulte <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="765f2-246">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
