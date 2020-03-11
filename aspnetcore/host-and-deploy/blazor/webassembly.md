---
title: Hospedar e implantar ASP.NET Core Blazor Webassembly
author: guardrex
description: Saiba como hospedar e implantar um aplicativo Blazor usando ASP.NET Core, redes de distribuição de conteúdo (CDN), servidores de arquivos e páginas do GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: eae12b266e91a30a47daf63ac77ba082c25225aa
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664097"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="653f4-103">Hospedar e implantar ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="653f4-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="653f4-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="653f4-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="653f4-105">Com o [modelo de hospedagem WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="653f4-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="653f4-106">O aplicativo Blazor, suas dependências e o tempo de execução do .NET são baixados para o navegador.</span><span class="sxs-lookup"><span data-stu-id="653f4-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="653f4-107">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="653f4-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="653f4-108">Há suporte para as seguintes estratégias de implantação:</span><span class="sxs-lookup"><span data-stu-id="653f4-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="653f4-109">O aplicativo Blazor é servido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="653f4-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="653f4-110">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="653f4-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="653f4-111">O aplicativo Blazor é colocado em um servidor Web ou serviço de hospedagem estática, em que o .NET não é usado para servir o aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="653f4-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="653f4-112">Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um aplicativo webassembly Blazor como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="653f4-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="653f4-113">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="653f4-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="653f4-114">O roteamento de solicitações para componentes de página em um aplicativo Webassembly Blazor não é tão simples quanto o roteamento de solicitações em um servidor Blazor, hospedado em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="653f4-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="653f4-115">Considere um aplicativo Webassembly Blazor com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="653f4-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="653f4-116">*Main. razor* &ndash; carrega na raiz do aplicativo e contém um link para o componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="653f4-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="653f4-117">*About. razor* &ndash; `About` componente.</span><span class="sxs-lookup"><span data-stu-id="653f4-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="653f4-118">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="653f4-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="653f4-119">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="653f4-119">The browser makes a request.</span></span>
1. <span data-ttu-id="653f4-120">A página padrão é retornada, que é geralmente é *index.html*.</span><span class="sxs-lookup"><span data-stu-id="653f4-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="653f4-121">A *index.html* inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="653f4-121">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="653f4-122">o roteador do Blazoré carregado e o componente `Main` Razor é renderizado.</span><span class="sxs-lookup"><span data-stu-id="653f4-122">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="653f4-123">Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o roteador Blazor interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` para `About` e serve para o próprio componente de `About` renderizado.</span><span class="sxs-lookup"><span data-stu-id="653f4-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="653f4-124">Todas as solicitações de pontos de extremidade internos *dentro do aplicativo Webassembly Blazor* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="653f4-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="653f4-125">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="653f4-125">The router handles the requests internally.</span></span>

<span data-ttu-id="653f4-126">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="653f4-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="653f4-127">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="653f4-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="653f4-128">Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="653f4-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="653f4-129">Quando *index. html* for retornado, o roteador Blazor do aplicativo assumirá e responderá com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="653f4-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="653f4-130">Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo *Web. config* publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="653f4-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="653f4-131">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="653f4-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="653f4-132">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="653f4-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="653f4-133">Uma *implantação hospedada* serve o aplicativo webassembly Blazor para os navegadores de um [aplicativo ASP.NET Core](xref:index) executado em um servidor Web.</span><span class="sxs-lookup"><span data-stu-id="653f4-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="653f4-134">O aplicativo Blazor está incluído no aplicativo ASP.NET Core na saída publicada para que os dois aplicativos sejam implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="653f4-134">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="653f4-135">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="653f4-135">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="653f4-136">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de **aplicativo WebassemblyBlazor** (`blazorwasm` modelo ao usar o comando [dotnet New](/dotnet/core/tools/dotnet-new) ) com a opção **Hosted** selecionada.</span><span class="sxs-lookup"><span data-stu-id="653f4-136">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected.</span></span>

<span data-ttu-id="653f4-137">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="653f4-137">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="653f4-138">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="653f4-138">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="653f4-139">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="653f4-139">Standalone deployment</span></span>

<span data-ttu-id="653f4-140">Uma *implantação autônoma* serve o aplicativo webassembly Blazor como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="653f4-140">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="653f4-141">Qualquer servidor de arquivos estático é capaz de atender o aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="653f4-141">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="653f4-142">Ativos de implantação autônomos são publicados na pasta */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist*.</span><span class="sxs-lookup"><span data-stu-id="653f4-142">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="653f4-143">IIS</span><span class="sxs-lookup"><span data-stu-id="653f4-143">IIS</span></span>

<span data-ttu-id="653f4-144">O IIS é um servidor de arquivos estático capaz para aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="653f4-144">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="653f4-145">Para configurar o IIS para hospedar Blazor, consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="653f4-145">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="653f4-146">Os ativos publicados são criados na pasta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="653f4-146">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="653f4-147">Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="653f4-147">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="653f4-148">web.config</span><span class="sxs-lookup"><span data-stu-id="653f4-148">web.config</span></span>

<span data-ttu-id="653f4-149">Quando um projeto de Blazor é publicado, um arquivo *Web. config* é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="653f4-149">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="653f4-150">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="653f4-150">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="653f4-151">*. dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="653f4-151">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="653f4-152">&ndash; *. json* `application/json`</span><span class="sxs-lookup"><span data-stu-id="653f4-152">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="653f4-153">*. wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="653f4-153">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="653f4-154">*. woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="653f4-154">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="653f4-155">*. woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="653f4-155">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="653f4-156">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="653f4-156">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="653f4-157">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="653f4-157">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="653f4-158">Atender ao subdiretório em que residem os ativos estáticos do aplicativo ( *{ASSEMBLY NAME}/dist/{PATH REQUESTED}* ).</span><span class="sxs-lookup"><span data-stu-id="653f4-158">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="653f4-159">Criar o roteamento de fallback do SPA, de modo que as solicitações de ativos, que não sejam arquivos, sejam redirecionadas ao documento padrão do aplicativo na pasta de ativos estáticos dele ( *{ASSEMBLY NAME}/dist/index.html*).</span><span class="sxs-lookup"><span data-stu-id="653f4-159">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="653f4-160">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="653f4-160">Install the URL Rewrite Module</span></span>

<span data-ttu-id="653f4-161">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="653f4-161">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="653f4-162">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="653f4-162">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="653f4-163">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="653f4-163">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="653f4-164">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="653f4-164">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="653f4-165">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="653f4-165">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="653f4-166">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="653f4-166">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="653f4-167">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="653f4-167">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="653f4-168">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="653f4-168">Copy the installer to the server.</span></span> <span data-ttu-id="653f4-169">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="653f4-169">Run the installer.</span></span> <span data-ttu-id="653f4-170">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="653f4-170">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="653f4-171">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="653f4-171">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="653f4-172">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="653f4-172">Configure the website</span></span>

<span data-ttu-id="653f4-173">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="653f4-173">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="653f4-174">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="653f4-174">The folder contains:</span></span>

* <span data-ttu-id="653f4-175">O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.</span><span class="sxs-lookup"><span data-stu-id="653f4-175">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="653f4-176">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="653f4-176">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="653f4-177">Hospedar como um subaplicativo do IIS</span><span class="sxs-lookup"><span data-stu-id="653f4-177">Host as an IIS sub-app</span></span>

<span data-ttu-id="653f4-178">Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="653f4-178">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="653f4-179">Desabilite o manipulador do módulo ASP.NET Core herdado.</span><span class="sxs-lookup"><span data-stu-id="653f4-179">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="653f4-180">Remova o manipulador no arquivo *Web. config* publicado do aplicativo Blazor adicionando uma seção `<handlers>` ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="653f4-180">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="653f4-181">Desabilite a herança da seção `<system.webServer>` do aplicativo raiz (pai) usando um elemento `<location>` com `inheritInChildApplications` definido como `false`:</span><span class="sxs-lookup"><span data-stu-id="653f4-181">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="653f4-182">A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="653f4-182">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="653f4-183">Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="653f4-183">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="653f4-184">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="653f4-184">Troubleshooting</span></span>

<span data-ttu-id="653f4-185">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="653f4-185">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="653f4-186">Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="653f4-186">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="653f4-187">Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de servir arquivos estáticos de Blazor.</span><span class="sxs-lookup"><span data-stu-id="653f4-187">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="653f4-188">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="653f4-188">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="653f4-189">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="653f4-189">Azure Storage</span></span>

<span data-ttu-id="653f4-190">A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos Blazor sem servidor.</span><span class="sxs-lookup"><span data-stu-id="653f4-190">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="653f4-191">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="653f4-191">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="653f4-192">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="653f4-192">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="653f4-193">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="653f4-193">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="653f4-194">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="653f4-194">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="653f4-195">Os componentes do Razor e outros pontos de extremidade que não são arquivos não residem em caminhos físicos no conteúdo estático armazenado pelo serviço de blob.</span><span class="sxs-lookup"><span data-stu-id="653f4-195">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="653f4-196">Quando uma solicitação para um desses recursos é recebida que o roteador de Blazor deve tratar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o **caminho do documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="653f4-196">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="653f4-197">O blob *index. html* é retornado e o roteador de Blazor carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="653f4-197">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="653f4-198">Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="653f4-198">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="653f4-199">Nginx</span><span class="sxs-lookup"><span data-stu-id="653f4-199">Nginx</span></span>

<span data-ttu-id="653f4-200">O arquivo *nginx.conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *index.html* sempre que ele não puder encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="653f4-200">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="653f4-201">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="653f4-201">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="653f4-202">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="653f4-202">Nginx in Docker</span></span>

<span data-ttu-id="653f4-203">Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine.</span><span class="sxs-lookup"><span data-stu-id="653f4-203">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="653f4-204">Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.</span><span class="sxs-lookup"><span data-stu-id="653f4-204">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="653f4-205">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="653f4-205">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="653f4-206">Apache</span><span class="sxs-lookup"><span data-stu-id="653f4-206">Apache</span></span>

<span data-ttu-id="653f4-207">Para implantar um aplicativo Webassembly Blazor no CentOS 7 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="653f4-207">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="653f4-208">Crie o arquivo de configuração do Apache.</span><span class="sxs-lookup"><span data-stu-id="653f4-208">Create the Apache configuration file.</span></span> <span data-ttu-id="653f4-209">O exemplo a seguir é um arquivo de configuração simplificado (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="653f4-209">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="653f4-210">Coloque o arquivo de configuração do Apache no diretório `/etc/httpd/conf.d/`, que é o diretório de configuração padrão do Apache no CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="653f4-210">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="653f4-211">Coloque os arquivos do aplicativo no diretório `/var/www/blazorapp` (o local especificado para `DocumentRoot` no arquivo de configuração).</span><span class="sxs-lookup"><span data-stu-id="653f4-211">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="653f4-212">Reinicie o serviço apache.</span><span class="sxs-lookup"><span data-stu-id="653f4-212">Restart the Apache service.</span></span>

<span data-ttu-id="653f4-213">Para obter mais informações, consulte [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="653f4-213">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="653f4-214">Páginas do GitHub</span><span class="sxs-lookup"><span data-stu-id="653f4-214">GitHub Pages</span></span>

<span data-ttu-id="653f4-215">Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="653f4-215">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="653f4-216">Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="653f4-216">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="653f4-217">Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="653f4-217">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="653f4-218">Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*.</span><span class="sxs-lookup"><span data-stu-id="653f4-218">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="653f4-219">Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="653f4-219">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="653f4-220">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="653f4-220">Host configuration values</span></span>

<span data-ttu-id="653f4-221">[Blazor aplicativos Webassembly](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="653f4-221">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="653f4-222">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="653f4-222">Content root</span></span>

<span data-ttu-id="653f4-223">O argumento `--contentroot` define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="653f4-223">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="653f4-224">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="653f4-224">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="653f4-225">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="653f4-225">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="653f4-226">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="653f4-226">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="653f4-227">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="653f4-227">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="653f4-228">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="653f4-228">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="653f4-229">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="653f4-229">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="653f4-230">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="653f4-230">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="653f4-231">Caminho base</span><span class="sxs-lookup"><span data-stu-id="653f4-231">Path base</span></span>

<span data-ttu-id="653f4-232">O argumento `--pathbase` define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a marca `<base>` `href` é definida como um caminho diferente de `/` para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="653f4-232">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="653f4-233">Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="653f4-233">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="653f4-234">Para obter mais informações, consulte [caminho base do aplicativo](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="653f4-234">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="653f4-235">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="653f4-235">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="653f4-236">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="653f4-236">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="653f4-237">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="653f4-237">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="653f4-238">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="653f4-238">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="653f4-239">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="653f4-239">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="653f4-240">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="653f4-240">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="653f4-241">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="653f4-241">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="653f4-242">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="653f4-242">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="653f4-243">URLs</span><span class="sxs-lookup"><span data-stu-id="653f4-243">URLs</span></span>

<span data-ttu-id="653f4-244">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="653f4-244">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="653f4-245">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="653f4-245">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="653f4-246">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="653f4-246">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="653f4-247">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="653f4-247">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="653f4-248">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="653f4-248">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="653f4-249">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="653f4-249">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="653f4-250">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="653f4-250">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="653f4-251">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="653f4-251">Configure the Linker</span></span>

Blazor<span data-ttu-id="653f4-252"> executa a vinculação de IL (linguagem intermediária) em cada compilação para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="653f4-252"> performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="653f4-253">A vinculação de assembly pode ser controlada no build.</span><span class="sxs-lookup"><span data-stu-id="653f4-253">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="653f4-254">Para obter mais informações, consulte <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="653f4-254">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
