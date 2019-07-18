---
title: Hospedar e implantar o ASP.NET Core Blazor no lado do cliente
author: guardrex
description: Veja como hospedar e implantar um aplicativo do Blazor usando o ASP.NET Core, a CDN (Rede de Distribuição de Conteúdo), servidores de arquivos e páginas do GitHub.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: be6b6c245440cb085a1a6b115f4f087306f7cc83
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308080"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a><span data-ttu-id="b4758-103">Hospedar e implantar o ASP.NET Core Blazor no lado do cliente</span><span class="sxs-lookup"><span data-stu-id="b4758-103">Host and deploy ASP.NET Core Blazor client-side</span></span>

<span data-ttu-id="b4758-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b4758-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b4758-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="b4758-105">Host configuration values</span></span>

<span data-ttu-id="b4758-106">Os aplicativos do Blazor que usam o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side) podem aceitar os seguintes valores de configuração do host como argumentos de linha de comando no tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="b4758-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="b4758-107">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="b4758-107">Content Root</span></span>

<span data-ttu-id="b4758-108">O argumento `--contentroot` define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="b4758-109">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="b4758-110">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="b4758-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b4758-111">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="b4758-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="b4758-112">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b4758-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b4758-113">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b4758-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="b4758-114">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="b4758-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b4758-115">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b4758-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="b4758-116">Caminho base</span><span class="sxs-lookup"><span data-stu-id="b4758-116">Path base</span></span>

<span data-ttu-id="b4758-117">O argumento `--pathbase` define o caminho base do aplicativo para um aplicativo executado localmente com um caminho virtual não raiz (a tag `href` `<base>` é definida como um caminho diferente de `/` para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="b4758-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="b4758-118">Nos exemplos a seguir, `/virtual-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="b4758-119">Para obter mais informações, confira a seção [Caminho base do aplicativo](#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="b4758-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b4758-120">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="b4758-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="b4758-121">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="b4758-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="b4758-122">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="b4758-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b4758-123">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="b4758-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="b4758-124">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b4758-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b4758-125">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b4758-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="b4758-126">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="b4758-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b4758-127">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b4758-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="b4758-128">URLs</span><span class="sxs-lookup"><span data-stu-id="b4758-128">URLs</span></span>

<span data-ttu-id="b4758-129">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="b4758-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="b4758-130">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="b4758-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b4758-131">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="b4758-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="b4758-132">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b4758-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b4758-133">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b4758-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="b4758-134">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="b4758-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b4758-135">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b4758-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="b4758-136">Implantação</span><span class="sxs-lookup"><span data-stu-id="b4758-136">Deployment</span></span>

<span data-ttu-id="b4758-137">Com o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side):</span><span class="sxs-lookup"><span data-stu-id="b4758-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side):</span></span>

* <span data-ttu-id="b4758-138">O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="b4758-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="b4758-139">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="b4758-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="b4758-140">Há suporte para todas as estratégias a seguir:</span><span class="sxs-lookup"><span data-stu-id="b4758-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="b4758-141">O aplicativo do Blazor é atendido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4758-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="b4758-142">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="b4758-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="b4758-143">O aplicativo do Blazor é colocado em um serviço ou um servidor Web de hospedagem estático, em que o .NET não é usado para atender ao aplicativo do Blazor.</span><span class="sxs-lookup"><span data-stu-id="b4758-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="b4758-144">Esta estratégia é abordada em [Implantação autônoma](#standalone-deployment).</span><span class="sxs-lookup"><span data-stu-id="b4758-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="b4758-145">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="b4758-145">Configure the Linker</span></span>

<span data-ttu-id="b4758-146">O Blazor executa a vinculação de IL (linguagem intermediária) em cada build para remover a IL desnecessária dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="b4758-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="b4758-147">A vinculação de assembly pode ser controlada no build.</span><span class="sxs-lookup"><span data-stu-id="b4758-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="b4758-148">Para obter mais informações, consulte <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="b4758-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="b4758-149">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="b4758-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="b4758-150">O roteamento de solicitações para componentes de página em um aplicativo do lado do cliente não é tão simples quanto o roteamento de solicitações para um aplicativo hospedado do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="b4758-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="b4758-151">Considere um aplicativo do lado do cliente com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="b4758-151">Consider a client-side app with two components:</span></span>

* <span data-ttu-id="b4758-152">*Main.razor* &ndash; É carregado na raiz do aplicativo e contém um link para o componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="b4758-152">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="b4758-153">*About.Razor* &ndash; componente `About`.</span><span class="sxs-lookup"><span data-stu-id="b4758-153">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="b4758-154">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="b4758-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="b4758-155">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="b4758-155">The browser makes a request.</span></span>
1. <span data-ttu-id="b4758-156">A página padrão é retornada, que é geralmente é *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b4758-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="b4758-157">A *index.html* inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="b4758-158">O roteador do Blazor é carregado e o componente `Main` Razor é renderizado.</span><span class="sxs-lookup"><span data-stu-id="b4758-158">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="b4758-159">Na página principal, a seleção do link para o componente `About` funciona no cliente porque o roteador do Blazor impede que o navegador faça uma solicitação na Internet para `www.contoso.com` de `About` e atende ao componente `About` renderizado.</span><span class="sxs-lookup"><span data-stu-id="b4758-159">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="b4758-160">Todas as solicitações de pontos de extremidades internos *no aplicativo do lado do cliente* funcionam da mesma maneira: Não são disparadas solicitações baseadas em navegador para os recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="b4758-160">All of the requests for internal endpoints *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="b4758-161">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="b4758-161">The router handles the requests internally.</span></span>

<span data-ttu-id="b4758-162">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="b4758-162">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="b4758-163">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="b4758-163">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="b4758-164">Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b4758-164">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="b4758-165">Quando a *index.html* for retornada, o roteador do lado do cliente do aplicativo assumirá o controle e responderá com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="b4758-165">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="b4758-166">Caminho base do aplicativo</span><span class="sxs-lookup"><span data-stu-id="b4758-166">App base path</span></span>

<span data-ttu-id="b4758-167">O *caminho base do aplicativo* é o caminho raiz do aplicativo virtual no servidor.</span><span class="sxs-lookup"><span data-stu-id="b4758-167">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="b4758-168">Por exemplo, um aplicativo que reside no servidor Contoso em uma pasta virtual em `/CoolApp/` é acessado em `https://www.contoso.com/CoolApp` e tem o caminho base virtual `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="b4758-168">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="b4758-169">Quando o caminho base do aplicativo é definido para o caminho virtual (`<base href="/CoolApp/">`), o aplicativo fica ciente de onde ele reside virtualmente no servidor.</span><span class="sxs-lookup"><span data-stu-id="b4758-169">By setting the app base path to the virtual path (`<base href="/CoolApp/">`), the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="b4758-170">O aplicativo pode usar o caminho base para construir URLs relativas à raiz do aplicativo de um componente que não esteja no diretório raiz.</span><span class="sxs-lookup"><span data-stu-id="b4758-170">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="b4758-171">Isso permite que os componentes que existem em diferentes níveis da estrutura de diretório criem links para outros recursos em locais em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-171">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="b4758-172">O caminho base do aplicativo também é usado para interceptar cliques em hiperlink em que o destino `href` do link está dentro do espaço do URI do caminho base do aplicativo. O roteador do Blazor manipula a navegação interna.</span><span class="sxs-lookup"><span data-stu-id="b4758-172">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="b4758-173">Em muitos cenários de hospedagem, o caminho virtual do servidor para o aplicativo é a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-173">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="b4758-174">Nesses casos, o caminho base do aplicativo é uma barra invertida (`<base href="/" />`), que é a configuração padrão de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-174">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="b4758-175">Em outros cenários de hospedagem, como Páginas do GitHub e diretórios virtuais ou subaplicativos do IIS, o caminho base do aplicativo precisa ser definido como o caminho virtual do servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-175">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="b4758-176">Para definir o caminho base do aplicativo, atualize a marca `<base>` encontrada nos elementos da marca `<head>` do arquivo *wwwroot/index.html*.</span><span class="sxs-lookup"><span data-stu-id="b4758-176">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="b4758-177">Defina o valor de atributo `href` como `/virtual-path/` (a barra à direita é necessária), em que `/virtual-path/` é o caminho raiz do aplicativo virtual completo no servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-177">Set the `href` attribute value to `/virtual-path/` (the trailing slash is required), where `/virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="b4758-178">No exemplo anterior, o caminho virtual é definido como `/CoolApp/`: `<base href="/CoolApp/">`.</span><span class="sxs-lookup"><span data-stu-id="b4758-178">In the preceding example, the virtual path is set to `/CoolApp/`: `<base href="/CoolApp/">`.</span></span>

<span data-ttu-id="b4758-179">No caso de um aplicativo com um caminho virtual não raiz configurado (por exemplo, `<base href="/CoolApp/">`), o aplicativo não consegue localizar seus recursos *quando é executado localmente*.</span><span class="sxs-lookup"><span data-stu-id="b4758-179">For an app with a non-root virtual path configured (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="b4758-180">Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b4758-180">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="b4758-181">Para passar o argumento base de caminho com o caminho raiz (`/`) ao executar o aplicativo localmente, execute o comando `dotnet run` no diretório do aplicativo com a opção `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="b4758-181">To pass the path base argument with the root path (`/`) when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

<span data-ttu-id="b4758-182">Para um aplicativo com um caminho virtual base equivalente a `/CoolApp/` (`<base href="/CoolApp/">`), o comando é:</span><span class="sxs-lookup"><span data-stu-id="b4758-182">For an app with a virtual base path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="b4758-183">O aplicativo responde localmente em `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="b4758-183">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="b4758-184">Confira mais informações na seção sobre [valor de configuração do host base de caminho](#path-base).</span><span class="sxs-lookup"><span data-stu-id="b4758-184">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="b4758-185">Se um aplicativo usar o [modelo de hospedagem do lado do cliente](xref:blazor/hosting-models#client-side) (com base no modelo de projeto **Blazor [lado do cliente]** , o modelo `blazor` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)) e for hospedado como um subaplicativo do IIS em um aplicativo do ASP.NET Core, será importante desabilitar o manipulador de módulo do ASP.NET Core herdado ou verificar se a seção `<handlers>` do aplicativo raiz (pai) no arquivo *web.config* não é herdada pelo subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-185">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side) (based on the **Blazor (client-side)** project template, the `blazor` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) and is hosted as an IIS sub-app in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="b4758-186">Remova o manipulador do arquivo *web.config* publicado do aplicativo adicionando uma seção `<handlers>` ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="b4758-186">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="b4758-187">Como alternativa, desabilite a herança da seção `<system.webServer>` do aplicativo raiz (pai) usando um elemento `<location>` com `inheritInChildApplications` definido como `false`:</span><span class="sxs-lookup"><span data-stu-id="b4758-187">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="b4758-188">A ação de remover o manipulador ou desabilitar a herança é realizada além da ação da configurar o caminho base do aplicativo, conforme descrito nesta seção.</span><span class="sxs-lookup"><span data-stu-id="b4758-188">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="b4758-189">Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="b4758-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="b4758-190">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4758-190">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="b4758-191">Uma *implantação hospedada* atende ao aplicativo do lado do cliente do Blazor para navegadores de um [aplicativo do ASP.NET Core](xref:index) que é executado em um servidor da web.</span><span class="sxs-lookup"><span data-stu-id="b4758-191">A *hosted deployment* serves the Blazor client-side app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="b4758-192">O aplicativo do Blazor é incluído com o aplicativo do ASP.NET Core na saída publicada para que ambos sejam implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="b4758-192">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="b4758-193">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4758-193">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b4758-194">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto **Blazor (hospedado no ASP.NET Core)** (modelo `blazorhosted` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="b4758-194">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="b4758-195">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="b4758-195">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="b4758-196">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="b4758-196">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="b4758-197">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="b4758-197">Standalone deployment</span></span>

<span data-ttu-id="b4758-198">Uma *implantação autônoma* atende ao aplicativo do lado do cliente do Blazor como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="b4758-198">A *standalone deployment* serves the Blazor client-side app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="b4758-199">Qualquer servidor de arquivos estático é capaz de atender ao aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="b4758-199">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="b4758-200">Ativos de implantação autônomos são publicados na pasta */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist*.</span><span class="sxs-lookup"><span data-stu-id="b4758-200">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="b4758-201">IIS</span><span class="sxs-lookup"><span data-stu-id="b4758-201">IIS</span></span>

<span data-ttu-id="b4758-202">O IIS é um servidor de arquivos estático com capacidade para aplicativos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="b4758-202">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="b4758-203">Para configurar o IIS para hospedar o Blazor, confira [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Criar um site estático no IIS).</span><span class="sxs-lookup"><span data-stu-id="b4758-203">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="b4758-204">Os ativos publicados são criados na pasta */bin/Release/{TARGET FRAMEWORK}/publish*.</span><span class="sxs-lookup"><span data-stu-id="b4758-204">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="b4758-205">Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="b4758-205">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="b4758-206">web.config</span><span class="sxs-lookup"><span data-stu-id="b4758-206">web.config</span></span>

<span data-ttu-id="b4758-207">Quando um projeto Blazor é publicado, um arquivo *web.config* é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="b4758-207">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="b4758-208">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="b4758-208">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="b4758-209">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="b4758-209">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="b4758-210">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="b4758-210">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="b4758-211">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="b4758-211">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="b4758-212">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b4758-212">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="b4758-213">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b4758-213">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="b4758-214">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="b4758-214">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="b4758-215">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="b4758-215">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="b4758-216">Atender ao subdiretório em que residem os ativos estáticos do aplicativo ( *{ASSEMBLY NAME}/dist/{PATH REQUESTED}* ).</span><span class="sxs-lookup"><span data-stu-id="b4758-216">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="b4758-217">Criar o roteamento de fallback do SPA, de modo que as solicitações de ativos, que não sejam arquivos, sejam redirecionadas ao documento padrão do aplicativo na pasta de ativos estáticos dele ( *{ASSEMBLY NAME}/dist/index.html*).</span><span class="sxs-lookup"><span data-stu-id="b4758-217">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="b4758-218">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="b4758-218">Install the URL Rewrite Module</span></span>

<span data-ttu-id="b4758-219">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="b4758-219">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="b4758-220">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="b4758-220">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="b4758-221">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="b4758-221">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="b4758-222">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="b4758-222">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="b4758-223">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="b4758-223">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="b4758-224">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="b4758-224">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="b4758-225">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="b4758-225">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="b4758-226">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="b4758-226">Copy the installer to the server.</span></span> <span data-ttu-id="b4758-227">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="b4758-227">Run the installer.</span></span> <span data-ttu-id="b4758-228">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="b4758-228">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="b4758-229">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="b4758-229">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="b4758-230">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="b4758-230">Configure the website</span></span>

<span data-ttu-id="b4758-231">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-231">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="b4758-232">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="b4758-232">The folder contains:</span></span>

* <span data-ttu-id="b4758-233">O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.</span><span class="sxs-lookup"><span data-stu-id="b4758-233">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="b4758-234">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b4758-234">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="b4758-235">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="b4758-235">Troubleshooting</span></span>

<span data-ttu-id="b4758-236">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="b4758-236">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="b4758-237">Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="b4758-237">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="b4758-238">Isso impede que o Gerenciador do IIS carregue a configuração do site e que o site atenda aos arquivos estáticos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="b4758-238">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="b4758-239">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b4758-239">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="b4758-240">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="b4758-240">Azure Storage</span></span>

<span data-ttu-id="b4758-241">A hospedagem de arquivo estático do Armazenamento do Azure permite a hospedagem de aplicativo Blazor sem servidor.</span><span class="sxs-lookup"><span data-stu-id="b4758-241">Azure Storage static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="b4758-242">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="b4758-242">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="b4758-243">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="b4758-243">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="b4758-244">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b4758-244">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="b4758-245">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b4758-245">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="b4758-246">Os componentes do Razor e outros pontos de extremidade que não são arquivos não residem em caminhos físicos no conteúdo estático armazenado pelo serviço de blob.</span><span class="sxs-lookup"><span data-stu-id="b4758-246">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="b4758-247">Quando uma solicitação para um desses recursos é recebida, a qual deve passar pelo Blazor, o erro *404 - Não Encontrado* gerado pelo serviço de blob encaminha a solicitação para o **Caminho do documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="b4758-247">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="b4758-248">O blob *index.html* retorna, e o roteador do Blazor carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="b4758-248">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="b4758-249">Para saber mais, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="b4758-249">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="b4758-250">Nginx</span><span class="sxs-lookup"><span data-stu-id="b4758-250">Nginx</span></span>

<span data-ttu-id="b4758-251">O arquivo *nginx.conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *index.html* sempre que ele não puder encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="b4758-251">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="b4758-252">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="b4758-252">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="b4758-253">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="b4758-253">Nginx in Docker</span></span>

<span data-ttu-id="b4758-254">Para hospedar o Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem do Nginx baseada no Alpine.</span><span class="sxs-lookup"><span data-stu-id="b4758-254">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="b4758-255">Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.</span><span class="sxs-lookup"><span data-stu-id="b4758-255">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="b4758-256">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="b4758-256">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="b4758-257">Páginas do GitHub</span><span class="sxs-lookup"><span data-stu-id="b4758-257">GitHub Pages</span></span>

<span data-ttu-id="b4758-258">Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b4758-258">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="b4758-259">Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="b4758-259">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="b4758-260">Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="b4758-260">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="b4758-261">Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b4758-261">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="b4758-262">Defina o valor de atributo `href` com o nome do repositório GitHub com uma barra à direita (por exemplo, `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="b4758-262">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
