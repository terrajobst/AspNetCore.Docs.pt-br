---
title: Hospedar e implantar o Blazor no lado do cliente
author: guardrex
description: Veja como hospedar e implantar um aplicativo do Blazor usando o ASP.NET Core, a CDN (Rede de Distribuição de Conteúdo), servidores de arquivos e páginas do GitHub.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: ea8ece266809913e32ac212bc55cb3c2499c234f
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874968"
---
# <a name="host-and-deploy-blazor-client-side"></a><span data-ttu-id="825d3-103">Hospedar e implantar o Blazor no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="825d3-103">Host and deploy Blazor client-side</span></span>

<span data-ttu-id="825d3-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="825d3-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="825d3-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="825d3-105">Host configuration values</span></span>

<span data-ttu-id="825d3-106">Os aplicativos do Blazor que usam o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side) podem aceitar os seguintes valores de configuração do host como argumentos de linha de comando no tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="825d3-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="825d3-107">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="825d3-107">Content Root</span></span>

<span data-ttu-id="825d3-108">O argumento `--contentroot` define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="825d3-109">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="825d3-110">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="825d3-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="825d3-111">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="825d3-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="825d3-112">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="825d3-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="825d3-113">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="825d3-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="825d3-114">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="825d3-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="825d3-115">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="825d3-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="825d3-116">Caminho base</span><span class="sxs-lookup"><span data-stu-id="825d3-116">Path base</span></span>

<span data-ttu-id="825d3-117">O argumento `--pathbase` define o caminho base do aplicativo para um aplicativo executado localmente com um caminho virtual não raiz (a tag `href` `<base>` é definida como um caminho diferente de `/` para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="825d3-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="825d3-118">Nos exemplos a seguir, `/virtual-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="825d3-119">Para obter mais informações, confira a seção [Caminho base do aplicativo](#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="825d3-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="825d3-120">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="825d3-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="825d3-121">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="825d3-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="825d3-122">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="825d3-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="825d3-123">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="825d3-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="825d3-124">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="825d3-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="825d3-125">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="825d3-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="825d3-126">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="825d3-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="825d3-127">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="825d3-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="825d3-128">URLs</span><span class="sxs-lookup"><span data-stu-id="825d3-128">URLs</span></span>

<span data-ttu-id="825d3-129">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="825d3-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="825d3-130">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="825d3-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="825d3-131">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="825d3-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="825d3-132">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="825d3-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="825d3-133">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="825d3-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="825d3-134">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="825d3-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="825d3-135">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="825d3-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="825d3-136">Implantação</span><span class="sxs-lookup"><span data-stu-id="825d3-136">Deployment</span></span>

<span data-ttu-id="825d3-137">Com o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side):</span><span class="sxs-lookup"><span data-stu-id="825d3-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side):</span></span>

* <span data-ttu-id="825d3-138">O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="825d3-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="825d3-139">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="825d3-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="825d3-140">Há suporte para todas as estratégias a seguir:</span><span class="sxs-lookup"><span data-stu-id="825d3-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="825d3-141">O aplicativo do Blazor é atendido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="825d3-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="825d3-142">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="825d3-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="825d3-143">O aplicativo do Blazor é colocado em um serviço ou um servidor Web de hospedagem estático, em que o .NET não é usado para atender ao aplicativo do Blazor.</span><span class="sxs-lookup"><span data-stu-id="825d3-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="825d3-144">Esta estratégia é abordada em [Implantação autônoma](#standalone-deployment).</span><span class="sxs-lookup"><span data-stu-id="825d3-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="825d3-145">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="825d3-145">Configure the Linker</span></span>

<span data-ttu-id="825d3-146">O Blazor executa a vinculação de IL (linguagem intermediária) em cada build para remover a IL desnecessária dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="825d3-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="825d3-147">A vinculação de assembly pode ser controlada no build.</span><span class="sxs-lookup"><span data-stu-id="825d3-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="825d3-148">Para obter mais informações, consulte <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="825d3-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="825d3-149">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="825d3-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="825d3-150">O roteamento de solicitações para componentes de página em um aplicativo do lado do cliente não é tão simples quanto o roteamento de solicitações para um aplicativo hospedado do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="825d3-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="825d3-151">Considere um aplicativo do lado do cliente com duas páginas:</span><span class="sxs-lookup"><span data-stu-id="825d3-151">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="825d3-152">**_Main.razor** &ndash; É carregado na raiz do aplicativo e contém um link para a página Sobre (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="825d3-152">**_Main.razor** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="825d3-153">**_About.razor** &ndash; Página Sobre.</span><span class="sxs-lookup"><span data-stu-id="825d3-153">**_About.razor** &ndash; About page.</span></span>

<span data-ttu-id="825d3-154">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="825d3-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="825d3-155">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="825d3-155">The browser makes a request.</span></span>
1. <span data-ttu-id="825d3-156">A página padrão é retornada, que é geralmente é *index.html*.</span><span class="sxs-lookup"><span data-stu-id="825d3-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="825d3-157">A *index.html* inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="825d3-158">O roteador do Blazor é carregado e a página Principal do Razor (*Main.razor*) é exibida.</span><span class="sxs-lookup"><span data-stu-id="825d3-158">Blazor's router loads, and the Razor Main page (*Main.razor*) is displayed.</span></span>

<span data-ttu-id="825d3-159">Na página Principal, é possível carregar a página Sobre selecionando o link para ela.</span><span class="sxs-lookup"><span data-stu-id="825d3-159">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="825d3-160">A seleção do link para a página Sobre funciona no cliente porque o roteador do Blazor impede que o navegador faça uma solicitação na Internet para `www.contoso.com` de `About` e atende à própria página Sobre.</span><span class="sxs-lookup"><span data-stu-id="825d3-160">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="825d3-161">Todas as solicitações de páginas internas *no aplicativo do lado do cliente* funcionam da mesma maneira: Não são disparadas solicitações baseadas em navegador para os recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="825d3-161">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="825d3-162">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="825d3-162">The router handles the requests internally.</span></span>

<span data-ttu-id="825d3-163">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="825d3-163">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="825d3-164">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="825d3-164">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="825d3-165">Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="825d3-165">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="825d3-166">Quando a *index.html* for retornada, o roteador do lado do cliente do aplicativo assumirá o controle e responderá com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="825d3-166">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="825d3-167">Caminho base do aplicativo</span><span class="sxs-lookup"><span data-stu-id="825d3-167">App base path</span></span>

<span data-ttu-id="825d3-168">O *caminho base do aplicativo* é o caminho raiz do aplicativo virtual no servidor.</span><span class="sxs-lookup"><span data-stu-id="825d3-168">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="825d3-169">Por exemplo, um aplicativo que reside no servidor Contoso em uma pasta virtual em `/CoolApp/` é acessado em `https://www.contoso.com/CoolApp` e tem o caminho base virtual `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="825d3-169">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="825d3-170">Quando o caminho base do aplicativo é definido para o caminho virtual (`<base href="/CoolApp/">`), o aplicativo fica ciente de onde ele reside virtualmente no servidor.</span><span class="sxs-lookup"><span data-stu-id="825d3-170">By setting the app base path to the virtual path (`<base href="/CoolApp/">`), the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="825d3-171">O aplicativo pode usar o caminho base para construir URLs relativas à raiz do aplicativo de um componente que não esteja no diretório raiz.</span><span class="sxs-lookup"><span data-stu-id="825d3-171">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="825d3-172">Isso permite que os componentes que existem em diferentes níveis da estrutura de diretório criem links para outros recursos em locais em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-172">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="825d3-173">O caminho base do aplicativo também é usado para interceptar cliques em hiperlink em que o destino `href` do link está dentro do espaço do URI do caminho base do aplicativo. O roteador do Blazor manipula a navegação interna.</span><span class="sxs-lookup"><span data-stu-id="825d3-173">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="825d3-174">Em muitos cenários de hospedagem, o caminho virtual do servidor para o aplicativo é a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-174">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="825d3-175">Nesses casos, o caminho base do aplicativo é uma barra invertida (`<base href="/" />`), que é a configuração padrão de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-175">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="825d3-176">Em outros cenários de hospedagem, como Páginas do GitHub e diretórios virtuais ou subaplicativos do IIS, o caminho base do aplicativo precisa ser definido como o caminho virtual do servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-176">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="825d3-177">Para definir o caminho base do aplicativo, atualize a marca `<base>` encontrada nos elementos da marca `<head>` do arquivo *wwwroot/index.html*.</span><span class="sxs-lookup"><span data-stu-id="825d3-177">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="825d3-178">Defina o valor de atributo `href` como `/virtual-path/` (a barra à direita é necessária), em que `/virtual-path/` é o caminho raiz do aplicativo virtual completo no servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-178">Set the `href` attribute value to `/virtual-path/` (the trailing slash is required), where `/virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="825d3-179">No exemplo anterior, o caminho virtual é definido como `/CoolApp/`: `<base href="/CoolApp/">`.</span><span class="sxs-lookup"><span data-stu-id="825d3-179">In the preceding example, the virtual path is set to `/CoolApp/`: `<base href="/CoolApp/">`.</span></span>

<span data-ttu-id="825d3-180">No caso de um aplicativo com um caminho virtual não raiz configurado (por exemplo, `<base href="/CoolApp/">`), o aplicativo não consegue localizar seus recursos *quando é executado localmente*.</span><span class="sxs-lookup"><span data-stu-id="825d3-180">For an app with a non-root virtual path configured (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="825d3-181">Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="825d3-181">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="825d3-182">Para passar o argumento base de caminho com o caminho raiz (`/`) ao executar o aplicativo localmente, execute o comando `dotnet run` no diretório do aplicativo com a opção `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="825d3-182">To pass the path base argument with the root path (`/`) when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

<span data-ttu-id="825d3-183">Para um aplicativo com um caminho virtual base equivalente a `/CoolApp/` (`<base href="/CoolApp/">`), o comando é:</span><span class="sxs-lookup"><span data-stu-id="825d3-183">For an app with a virtual base path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="825d3-184">O aplicativo responde localmente em `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="825d3-184">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="825d3-185">Confira mais informações na seção sobre [valor de configuração do host base de caminho](#path-base).</span><span class="sxs-lookup"><span data-stu-id="825d3-185">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="825d3-186">Se um aplicativo usar o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side) (com base no modelo de projeto **Blazor**; o modelo `blazor` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)) e for hospedado como um subaplicativo do IIS em um aplicativo do ASP.NET Core, será importante desabilitar o manipulador de módulo do ASP.NET Core herdado ou verificar se a seção `<handlers>` do aplicativo raiz (pai) no arquivo *web.config* não é herdada pelo subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-186">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side) (based on the **Blazor** project template; the `blazor` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="825d3-187">Remova o manipulador do arquivo *web.config* publicado do aplicativo adicionando uma seção `<handlers>` ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="825d3-187">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="825d3-188">Como alternativa, desabilite a herança da seção `<system.webServer>` do aplicativo raiz (pai) usando um elemento `<location>` com `inheritInChildApplications` definido como `false`:</span><span class="sxs-lookup"><span data-stu-id="825d3-188">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="825d3-189">A ação de remover o manipulador ou desabilitar a herança é realizada além da ação da configurar o caminho base do aplicativo, conforme descrito nesta seção.</span><span class="sxs-lookup"><span data-stu-id="825d3-189">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="825d3-190">Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="825d3-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="825d3-191">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="825d3-191">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="825d3-192">Uma *implantação hospedada* atende ao aplicativo do Blazor do lado do cliente para navegadores de um [aplicativo do ASP.NET Core](xref:index) que é executado em um servidor.</span><span class="sxs-lookup"><span data-stu-id="825d3-192">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="825d3-193">O aplicativo do Blazor é incluído com o aplicativo do ASP.NET Core na saída publicada para que ambos sejam implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="825d3-193">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="825d3-194">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="825d3-194">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="825d3-195">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto **Blazor (hospedado no ASP.NET Core)** (modelo `blazorhosted` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="825d3-195">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="825d3-196">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="825d3-196">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="825d3-197">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="825d3-197">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="825d3-198">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="825d3-198">Standalone deployment</span></span>

<span data-ttu-id="825d3-199">Uma *implantação autônoma* atende ao aplicativo do Blazor do lado do cliente como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="825d3-199">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="825d3-200">Não é usado é um servidor Web para atender ao aplicativo do Blazor.</span><span class="sxs-lookup"><span data-stu-id="825d3-200">A web server isn't used to serve the Blazor app.</span></span>

### <a name="iis"></a><span data-ttu-id="825d3-201">IIS</span><span class="sxs-lookup"><span data-stu-id="825d3-201">IIS</span></span>

<span data-ttu-id="825d3-202">O IIS é um servidor de arquivos estático com capacidade para aplicativos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="825d3-202">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="825d3-203">Para configurar o IIS para hospedar o Blazor, confira [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Criar um site estático no IIS).</span><span class="sxs-lookup"><span data-stu-id="825d3-203">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="825d3-204">Os ativos publicados são criados na pasta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="825d3-204">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="825d3-205">Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="825d3-205">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="825d3-206">web.config</span><span class="sxs-lookup"><span data-stu-id="825d3-206">web.config</span></span>

<span data-ttu-id="825d3-207">Quando um projeto Blazor é publicado, um arquivo *web.config* é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="825d3-207">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="825d3-208">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="825d3-208">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="825d3-209">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="825d3-209">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="825d3-210">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="825d3-210">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="825d3-211">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="825d3-211">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="825d3-212">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="825d3-212">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="825d3-213">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="825d3-213">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="825d3-214">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="825d3-214">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="825d3-215">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="825d3-215">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="825d3-216">Atender ao subdiretório em que residem os ativos estáticos do aplicativo (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span><span class="sxs-lookup"><span data-stu-id="825d3-216">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="825d3-217">Criar o roteamento de fallback do SPA, de modo que as solicitações de ativos, que não sejam arquivos, sejam redirecionadas ao documento padrão do aplicativo na pasta de ativos estáticos dele (*{ASSEMBLY NAME}/dist/index.html*).</span><span class="sxs-lookup"><span data-stu-id="825d3-217">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="825d3-218">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="825d3-218">Install the URL Rewrite Module</span></span>

<span data-ttu-id="825d3-219">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="825d3-219">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="825d3-220">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="825d3-220">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="825d3-221">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="825d3-221">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="825d3-222">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="825d3-222">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="825d3-223">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="825d3-223">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="825d3-224">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="825d3-224">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="825d3-225">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="825d3-225">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="825d3-226">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="825d3-226">Copy the installer to the server.</span></span> <span data-ttu-id="825d3-227">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="825d3-227">Run the installer.</span></span> <span data-ttu-id="825d3-228">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="825d3-228">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="825d3-229">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="825d3-229">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="825d3-230">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="825d3-230">Configure the website</span></span>

<span data-ttu-id="825d3-231">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-231">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="825d3-232">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="825d3-232">The folder contains:</span></span>

* <span data-ttu-id="825d3-233">O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.</span><span class="sxs-lookup"><span data-stu-id="825d3-233">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="825d3-234">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="825d3-234">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="825d3-235">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="825d3-235">Troubleshooting</span></span>

<span data-ttu-id="825d3-236">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="825d3-236">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="825d3-237">Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="825d3-237">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="825d3-238">Isso impede que o Gerenciador do IIS carregue a configuração do site e que o site atenda aos arquivos estáticos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="825d3-238">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="825d3-239">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="825d3-239">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="nginx"></a><span data-ttu-id="825d3-240">Nginx</span><span class="sxs-lookup"><span data-stu-id="825d3-240">Nginx</span></span>

<span data-ttu-id="825d3-241">O arquivo *nginx.conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *index.html* sempre que ele não puder encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="825d3-241">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="825d3-242">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="825d3-242">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="825d3-243">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="825d3-243">Nginx in Docker</span></span>

<span data-ttu-id="825d3-244">Para hospedar o Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem do Nginx baseada no Alpine.</span><span class="sxs-lookup"><span data-stu-id="825d3-244">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="825d3-245">Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.</span><span class="sxs-lookup"><span data-stu-id="825d3-245">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="825d3-246">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="825d3-246">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="825d3-247">Páginas do GitHub</span><span class="sxs-lookup"><span data-stu-id="825d3-247">GitHub Pages</span></span>

<span data-ttu-id="825d3-248">Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="825d3-248">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="825d3-249">Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="825d3-249">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="825d3-250">Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="825d3-250">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="825d3-251">Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*.</span><span class="sxs-lookup"><span data-stu-id="825d3-251">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="825d3-252">Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="825d3-252">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
