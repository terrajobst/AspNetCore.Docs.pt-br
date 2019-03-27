---
title: Hospedar e implantar Componentes Razor
author: guardrex
description: 'Descubra como hospedar e implantar aplicativos dos Componentes Razor e do Blazor usando o ASP.NET Core, a CDN (Rede de Distribuição de Conteúdo), servidores de arquivos e páginas do GitHub.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/22/2019
uid: host-and-deploy/razor-components/index
---
# <a name="host-and-deploy-razor-components"></a><span data-ttu-id="714ec-103">Hospedar e implantar Componentes Razor</span><span class="sxs-lookup"><span data-stu-id="714ec-103">Host and deploy Razor Components</span></span>

<span data-ttu-id="714ec-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="714ec-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="714ec-105">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="714ec-105">Publish the app</span></span>

<span data-ttu-id="714ec-106">Os aplicativos são publicados para implantação na configuração de versão com o comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="714ec-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="714ec-107">Um IDE pode manipular a execução do comando `dotnet publish` automaticamente usando recursos de publicação internos, para que não seja necessário executar o comando manualmente usando um prompt de comando, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="714ec-107">An IDE may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="714ec-108">O `dotnet publish` dispara uma [restauração](/dotnet/core/tools/dotnet-restore) das dependências do projeto e [compila](/dotnet/core/tools/dotnet-build) o projeto antes de criar os ativos para implantação.</span><span class="sxs-lookup"><span data-stu-id="714ec-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="714ec-109">Como parte do processo de build, os assemblies e métodos não usados são removidos para reduzir o tamanho de download do aplicativo e os tempos de carregamento.</span><span class="sxs-lookup"><span data-stu-id="714ec-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="714ec-110">A implantação é criada na pasta */bin/Release/&lt;target-framework&gt;/publish*.</span><span class="sxs-lookup"><span data-stu-id="714ec-110">The deployment is created in the */bin/Release/&lt;target-framework&gt;/publish* folder.</span></span>

<span data-ttu-id="714ec-111">Os ativos na pasta *publish* são implantados no servidor Web.</span><span class="sxs-lookup"><span data-stu-id="714ec-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="714ec-112">A implantação pode ser um processo manual ou automatizado, dependendo das ferramentas de desenvolvimento em uso.</span><span class="sxs-lookup"><span data-stu-id="714ec-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="714ec-113">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="714ec-113">Host configuration values</span></span>

<span data-ttu-id="714ec-114">Os aplicativos dos Componentes Razor que usam o [modelo de hospedagem do lado do servidor](xref:razor-components/hosting-models#server-side-hosting-model) podem aceitar os [valores de configuração do host da Web](xref:fundamentals/host/web-host#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="714ec-114">Razor Components apps that use the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model) can accept [Web Host configuration values](xref:fundamentals/host/web-host#host-configuration-values).</span></span>

<span data-ttu-id="714ec-115">Os aplicativos do Blazor que usam o [modelo de hospedagem do lado do cliente](xref:razor-components/hosting-models#client-side-hosting-model) podem aceitar os seguintes valores de configuração do host como argumentos de linha de comando no tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="714ec-115">Blazor apps that use the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="714ec-116">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="714ec-116">Content Root</span></span>

<span data-ttu-id="714ec-117">O argumento `--contentroot` define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-117">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span>

* <span data-ttu-id="714ec-118">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="714ec-118">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="714ec-119">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="714ec-119">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* <span data-ttu-id="714ec-120">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="714ec-120">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="714ec-121">Essa configuração é obtida ao executar o aplicativo com o Depurador do Visual Studio e ao executar o aplicativo em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="714ec-121">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* <span data-ttu-id="714ec-122">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="714ec-122">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="714ec-123">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="714ec-123">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a><span data-ttu-id="714ec-124">Caminho base</span><span class="sxs-lookup"><span data-stu-id="714ec-124">Path base</span></span>

<span data-ttu-id="714ec-125">O argumento `--pathbase` define o caminho base do aplicativo para um aplicativo executado localmente com um caminho virtual não raiz (a tag `href` `<base>` é definida como um caminho diferente de `/` para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="714ec-125">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="714ec-126">Para obter mais informações, confira a seção [Caminho base do aplicativo](#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="714ec-126">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="714ec-127">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="714ec-127">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="714ec-128">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/" />` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="714ec-128">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/" />` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="714ec-129">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="714ec-129">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="714ec-130">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="714ec-130">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* <span data-ttu-id="714ec-131">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="714ec-131">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="714ec-132">Essa configuração é obtida ao executar o aplicativo com o Depurador do Visual Studio e ao executar o aplicativo em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="714ec-132">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* <span data-ttu-id="714ec-133">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="714ec-133">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="714ec-134">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="714ec-134">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a><span data-ttu-id="714ec-135">URLs</span><span class="sxs-lookup"><span data-stu-id="714ec-135">URLs</span></span>

<span data-ttu-id="714ec-136">O argumento `--urls` indica os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="714ec-136">The `--urls` argument indicates the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="714ec-137">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="714ec-137">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="714ec-138">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="714ec-138">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="714ec-139">Adicione uma entrada ao arquivo *launchSettings.json* do aplicativo no perfil do **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="714ec-139">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="714ec-140">Essa configuração é obtida ao executar o aplicativo com o Depurador do Visual Studio e ao executar o aplicativo em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="714ec-140">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="714ec-141">No Visual Studio, especifique o argumento em **Propriedades** > **Depurar** > **Argumentos de aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="714ec-141">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="714ec-142">A configuração do argumento na página de propriedades do Visual Studio adiciona o argumento ao arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="714ec-142">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a><span data-ttu-id="714ec-143">Implantar um aplicativo do Blazor do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="714ec-143">Deploy a client-side Blazor app</span></span>

<span data-ttu-id="714ec-144">Com o [modelo de hospedagem do lado do cliente](xref:razor-components/hosting-models#client-side-hosting-model):</span><span class="sxs-lookup"><span data-stu-id="714ec-144">With the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="714ec-145">O aplicativo do Blazor, suas dependências e o tempo de execução do .NET são baixados no navegador.</span><span class="sxs-lookup"><span data-stu-id="714ec-145">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="714ec-146">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="714ec-146">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="714ec-147">Há suporte para todas as estratégias a seguir:</span><span class="sxs-lookup"><span data-stu-id="714ec-147">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="714ec-148">O aplicativo do Blazor é atendido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="714ec-148">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="714ec-149">Abordado na seção [Implantação hospedada do Blazor do lado do cliente com o ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="714ec-149">Covered in the [Client-side Blazor hosted deployment with ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="714ec-150">O aplicativo do Blazor é colocado em um serviço ou um servidor Web de hospedagem estático, em que o .NET não é usado para atender ao aplicativo do Blazor.</span><span class="sxs-lookup"><span data-stu-id="714ec-150">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="714ec-151">Abordado na seção [Implantação autônoma do Blazor do lado do cliente](#client-side-blazor-standalone-deployment).</span><span class="sxs-lookup"><span data-stu-id="714ec-151">Covered in the [Client-side Blazor standalone deployment](#client-side-blazor-standalone-deployment) section.</span></span>

### <a name="configure-the-linker"></a><span data-ttu-id="714ec-152">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="714ec-152">Configure the Linker</span></span>

<span data-ttu-id="714ec-153">O Blazor executa a vinculação de IL (linguagem intermediária) em cada build para remover a IL desnecessária dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="714ec-153">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="714ec-154">Você pode controlar a vinculação de assembly no build.</span><span class="sxs-lookup"><span data-stu-id="714ec-154">You can control assembly linking on build.</span></span> <span data-ttu-id="714ec-155">Para obter mais informações, consulte <xref:host-and-deploy/razor-components/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="714ec-155">For more information, see <xref:host-and-deploy/razor-components/configure-linker>.</span></span>

### <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="714ec-156">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="714ec-156">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="714ec-157">O roteamento de solicitações para componentes de página em um aplicativo do lado do cliente não é tão simples quanto o roteamento de solicitações para um aplicativo hospedado do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="714ec-157">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="714ec-158">Considere um aplicativo do lado do cliente com duas páginas:</span><span class="sxs-lookup"><span data-stu-id="714ec-158">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="714ec-159">**_Main.cshtml_** &ndash; É carregado na raiz do aplicativo e contém um link para a página Sobre (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="714ec-159">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="714ec-160">**_About.cshtml_** &ndash; Página Sobre.</span><span class="sxs-lookup"><span data-stu-id="714ec-160">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="714ec-161">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="714ec-161">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="714ec-162">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="714ec-162">The browser makes a request.</span></span>
1. <span data-ttu-id="714ec-163">A página padrão é retornada, que é geralmente é *index.html*.</span><span class="sxs-lookup"><span data-stu-id="714ec-163">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="714ec-164">A *index.html* inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-164">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="714ec-165">O roteador do Blazor é carregado e a página Principal do Razor (*Main.cshtml*) é exibida.</span><span class="sxs-lookup"><span data-stu-id="714ec-165">Blazor's router loads and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="714ec-166">Na página Principal, é possível carregar a página Sobre selecionando o link para ela.</span><span class="sxs-lookup"><span data-stu-id="714ec-166">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="714ec-167">A seleção do link para a página Sobre funciona no cliente porque o roteador do Blazor impede que o navegador faça uma solicitação na Internet para `www.contoso.com` de `About` e atende à própria página Sobre.</span><span class="sxs-lookup"><span data-stu-id="714ec-167">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="714ec-168">Todas as solicitações de páginas internas *no aplicativo do lado do cliente* funcionam da mesma maneira: Não são disparadas solicitações baseadas em navegador para os recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="714ec-168">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="714ec-169">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="714ec-169">The router handles the requests internally.</span></span>

<span data-ttu-id="714ec-170">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="714ec-170">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="714ec-171">Esse recurso não existe no host do aplicativo na Internet, portanto, uma resposta *404 Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="714ec-171">No such resource exists on the app's Internet host, so a *404 Not found* response is returned.</span></span>

<span data-ttu-id="714ec-172">Como os navegadores fazem solicitações aos hosts baseados na Internet de páginas do lado do cliente, os servidores Web e os serviços de hospedagem precisam reescrever todas as solicitações de recursos que não estão fisicamente no servidor para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="714ec-172">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="714ec-173">Quando a *index.html* for retornada, o roteador do lado do cliente do aplicativo assumirá o controle e responderá com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="714ec-173">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

### <a name="app-base-path"></a><span data-ttu-id="714ec-174">Caminho base do aplicativo</span><span class="sxs-lookup"><span data-stu-id="714ec-174">App base path</span></span>

<span data-ttu-id="714ec-175">O caminho base do aplicativo é o caminho raiz do aplicativo virtual no servidor.</span><span class="sxs-lookup"><span data-stu-id="714ec-175">The app base path is the virtual app root path on the server.</span></span> <span data-ttu-id="714ec-176">Por exemplo, um aplicativo que reside no servidor Contoso em uma pasta virtual em `/CoolApp/` é acessado em `https://www.contoso.com/CoolApp` e tem o caminho base virtual `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="714ec-176">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="714ec-177">Quando o caminho base do aplicativo é definido como `CoolApp/`, o aplicativo fica ciente de onde ele reside virtualmente no servidor.</span><span class="sxs-lookup"><span data-stu-id="714ec-177">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="714ec-178">O aplicativo pode usar o caminho base para construir URLs relativas à raiz do aplicativo de um componente que não esteja no diretório raiz.</span><span class="sxs-lookup"><span data-stu-id="714ec-178">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="714ec-179">Isso permite que os componentes que existem em diferentes níveis da estrutura de diretório criem links para outros recursos em locais em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-179">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="714ec-180">O caminho base do aplicativo também é usado para interceptar cliques em hiperlink em que o destino `href` do link está dentro do espaço do URI do caminho base do aplicativo. O roteador do Blazor manipula a navegação interna.</span><span class="sxs-lookup"><span data-stu-id="714ec-180">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="714ec-181">Em muitos cenários de hospedagem, o caminho virtual do servidor para o aplicativo é a raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-181">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="714ec-182">Nesses casos, o caminho base do aplicativo é uma barra invertida (`<base href="/" />`), que é a configuração padrão de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-182">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="714ec-183">Em outros cenários de hospedagem, como Páginas do GitHub e diretórios virtuais ou subaplicativos do IIS, o caminho base do aplicativo precisa ser definido como o caminho virtual do servidor para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-183">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="714ec-184">Para definir o caminho base do aplicativo, adicione ou atualize a tag `<base>` na *index.html* encontrada nos elementos da tag `<head>`.</span><span class="sxs-lookup"><span data-stu-id="714ec-184">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="714ec-185">Defina o valor de atributo `href` como `<virtual-path>/` (a barra à direita é necessária), em que `<virtual-path>/` é o caminho raiz do aplicativo virtual completo no servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-185">Set the `href` attribute value to `<virtual-path>/` (the trailing slash is required), where `<virtual-path>/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="714ec-186">No exemplo anterior, o caminho virtual é definido como `CoolApp/`: `<base href="CoolApp/" />`.</span><span class="sxs-lookup"><span data-stu-id="714ec-186">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/" />`.</span></span>

<span data-ttu-id="714ec-187">No caso de um aplicativo com um caminho virtual não raiz configurado (por exemplo, `<base href="CoolApp/" />`), o aplicativo não consegue localizar seus recursos *quando é executado localmente*.</span><span class="sxs-lookup"><span data-stu-id="714ec-187">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/" />`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="714ec-188">Para superar esse problema durante o desenvolvimento e os testes locais, você pode fornecer um argumento *base de caminho* que corresponde ao valor de `href` da tag `<base>` no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="714ec-188">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="714ec-189">Para passar o argumento base de caminho com o caminho raiz (`/`) ao executar o aplicativo localmente, execute o seguinte comando no diretório do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="714ec-189">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="714ec-190">O aplicativo responde localmente em `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="714ec-190">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="714ec-191">Para obter mais informações, confira a seção de [valor de configuração do host base de caminho](#path-base).</span><span class="sxs-lookup"><span data-stu-id="714ec-191">For more information, see the [path base host configuration value](#path-base) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="714ec-192">Se um aplicativo usa o [modelo de hospedagem do lado do cliente](xref:razor-components/hosting-models#client-side-hosting-model) (com base no modelo de projeto **Blazor**) e é hospedado como um subaplicativo do IIS em um aplicativo do ASP.NET Core, é importante desabilitar o manipulador de módulo do ASP.NET Core herdado ou verificar se a seção `<handlers>` do aplicativo raiz (pai) no arquivo *web.config* não é herdada pelo subaplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-192">If an app uses the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>
>
> <span data-ttu-id="714ec-193">Remova o manipulador do arquivo *web.config* publicado do aplicativo adicionando uma seção `<handlers>` ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="714ec-193">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> <span data-ttu-id="714ec-194">Como alternativa, desabilite a herança da seção `<system.webServer>` do aplicativo raiz (pai) usando um elemento `<location>` com `inheritInChildApplications` definido como `false`:</span><span class="sxs-lookup"><span data-stu-id="714ec-194">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> <span data-ttu-id="714ec-195">A ação de remover o manipulador ou desabilitar a herança é realizada além da ação da configurar o caminho base do aplicativo, conforme descrito nesta seção.</span><span class="sxs-lookup"><span data-stu-id="714ec-195">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="714ec-196">Defina o caminho base do aplicativo no arquivo *index.html* do aplicativo do alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="714ec-196">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a><span data-ttu-id="714ec-197">Implantação hospedada do Blazor do lado do cliente com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="714ec-197">Client-side Blazor hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="714ec-198">Uma *implantação hospedada* atende ao aplicativo do Blazor do lado do cliente para navegadores de um [aplicativo do ASP.NET Core](xref:index) que é executado em um servidor.</span><span class="sxs-lookup"><span data-stu-id="714ec-198">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="714ec-199">O aplicativo do Blazor é incluído com o aplicativo do ASP.NET Core na saída publicada para que ambos sejam implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="714ec-199">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="714ec-200">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="714ec-200">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="714ec-201">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto **Blazor (hospedado no ASP.NET Core)** (modelo `blazorhosted` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="714ec-201">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="714ec-202">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="714ec-202">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="714ec-203">Para obter informações de como implantar o Serviço de Aplicativo do Azure, confira os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="714ec-203">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>

<span data-ttu-id="714ec-204">Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="714ec-204">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

### <a name="client-side-blazor-standalone-deployment"></a><span data-ttu-id="714ec-205">Implantação autônoma do Blazor do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="714ec-205">Client-side Blazor standalone deployment</span></span>

<span data-ttu-id="714ec-206">Uma *implantação autônoma* atende ao aplicativo do Blazor do lado do cliente como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="714ec-206">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="714ec-207">Não é usado é um servidor Web para atender ao aplicativo do Blazor.</span><span class="sxs-lookup"><span data-stu-id="714ec-207">A web server isn't used to serve the Blazor app.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a><span data-ttu-id="714ec-208">Hospedagem autônoma do Blazor do lado do cliente com o IIS</span><span class="sxs-lookup"><span data-stu-id="714ec-208">Client-side Blazor standalone hosting with IIS</span></span>

<span data-ttu-id="714ec-209">O IIS é um servidor de arquivos estático com capacidade para aplicativos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="714ec-209">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="714ec-210">Para configurar o IIS para hospedar o Blazor, confira [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Criar um site estático no IIS).</span><span class="sxs-lookup"><span data-stu-id="714ec-210">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="714ec-211">Os ativos publicados são criados na pasta *\\bin\\Release\\&lt;target-framework&gt;\\publish*.</span><span class="sxs-lookup"><span data-stu-id="714ec-211">Published assets are created in the *\\bin\\Release\\&lt;target-framework&gt;\\publish* folder.</span></span> <span data-ttu-id="714ec-212">Hospede o conteúdo da pasta *publish* no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="714ec-212">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

<span data-ttu-id="714ec-213">**web.config**</span><span class="sxs-lookup"><span data-stu-id="714ec-213">**web.config**</span></span>

<span data-ttu-id="714ec-214">Quando um projeto Blazor é publicado, um arquivo *web.config* é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="714ec-214">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="714ec-215">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="714ec-215">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="714ec-216">*\*.dll*: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="714ec-216">*\*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="714ec-217">*\*.json*: `application/json`</span><span class="sxs-lookup"><span data-stu-id="714ec-217">*\*.json*: `application/json`</span></span>
  * <span data-ttu-id="714ec-218">*\*.wasm*: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="714ec-218">*\*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="714ec-219">*\*.woff*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="714ec-219">*\*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="714ec-220">*\*.woff2*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="714ec-220">*\*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="714ec-221">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="714ec-221">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="714ec-222">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="714ec-222">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="714ec-223">Atender ao subdiretório em que residem os ativos estáticos do aplicativo (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).</span><span class="sxs-lookup"><span data-stu-id="714ec-223">Serve the sub-directory where the app's static assets reside (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).</span></span>
  * <span data-ttu-id="714ec-224">Criar o roteamento de fallback do SPA, de modo que as solicitações de ativos que não sejam arquivos sejam redirecionadas ao documento padrão do aplicativo na pasta de ativos estáticos dele (*&lt;assembly_name&gt;\\dist\\index.html*).</span><span class="sxs-lookup"><span data-stu-id="714ec-224">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*&lt;assembly_name&gt;\\dist\\index.html*).</span></span>

<span data-ttu-id="714ec-225">**Instalar o Módulo de Reescrita de URL**</span><span class="sxs-lookup"><span data-stu-id="714ec-225">**Install the URL Rewrite Module**</span></span>

<span data-ttu-id="714ec-226">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="714ec-226">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="714ec-227">O módulo não está instalado por padrão e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="714ec-227">The module isn't installed by default, and it isn't available for install as an Web Server (IIS) role service feature.</span></span> <span data-ttu-id="714ec-228">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="714ec-228">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="714ec-229">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="714ec-229">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="714ec-230">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="714ec-230">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="714ec-231">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="714ec-231">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="714ec-232">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="714ec-232">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="714ec-233">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="714ec-233">Copy the installer to the server.</span></span> <span data-ttu-id="714ec-234">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="714ec-234">Run the installer.</span></span> <span data-ttu-id="714ec-235">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="714ec-235">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="714ec-236">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="714ec-236">A server restart isn't required after the install completes.</span></span>

<span data-ttu-id="714ec-237">**Configurar o site**</span><span class="sxs-lookup"><span data-stu-id="714ec-237">**Configure the website**</span></span>

<span data-ttu-id="714ec-238">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-238">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="714ec-239">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="714ec-239">The folder contains:</span></span>

* <span data-ttu-id="714ec-240">O arquivo *web.config* que o IIS usa para configurar o site, incluindo as regras de redirecionamento e os tipos de conteúdo do arquivo necessários.</span><span class="sxs-lookup"><span data-stu-id="714ec-240">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="714ec-241">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="714ec-241">The app's static asset folder.</span></span>

<span data-ttu-id="714ec-242">**Solução de problemas**</span><span class="sxs-lookup"><span data-stu-id="714ec-242">**Troubleshooting**</span></span>

<span data-ttu-id="714ec-243">Se um *500 Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Reescrita de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="714ec-243">If a *500 Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="714ec-244">Quando o módulo não estiver instalado, o arquivo *web.config* não poderá ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="714ec-244">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="714ec-245">Isso impede que o Gerenciador do IIS carregue a configuração do site e que o site atenda aos arquivos estáticos do Blazor.</span><span class="sxs-lookup"><span data-stu-id="714ec-245">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="714ec-246">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="714ec-246">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a><span data-ttu-id="714ec-247">Hospedagem autônoma do Blazor do lado do cliente com o Nginx</span><span class="sxs-lookup"><span data-stu-id="714ec-247">Client-side Blazor standalone hosting with Nginx</span></span>

<span data-ttu-id="714ec-248">O arquivo *nginx.conf* a seguir é simplificado para mostrar como configurar o Nginx para enviar o arquivo *Index.html* sempre que ele não puder encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="714ec-248">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *Index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

<span data-ttu-id="714ec-249">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="714ec-249">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a><span data-ttu-id="714ec-250">Hospedagem autônoma do Blazor do lado do cliente no Docker</span><span class="sxs-lookup"><span data-stu-id="714ec-250">Client-side Blazor standalone hosting with Nginx in Docker</span></span>

<span data-ttu-id="714ec-251">Para hospedar o Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem do Nginx baseada no Alpine.</span><span class="sxs-lookup"><span data-stu-id="714ec-251">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="714ec-252">Atualize o Dockerfile para copiar o arquivo *nginx.config* no contêiner.</span><span class="sxs-lookup"><span data-stu-id="714ec-252">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="714ec-253">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="714ec-253">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a><span data-ttu-id="714ec-254">Hospedagem autônoma do Blazor do lado do cliente com as Páginas do GitHub</span><span class="sxs-lookup"><span data-stu-id="714ec-254">Client-side Blazor standalone hosting with GitHub Pages</span></span>

<span data-ttu-id="714ec-255">Para lidar com as reescritas de URL, adicione um arquivo *404.html* com um script que manipule o redirecionamento de solicitação para a página *index.html*.</span><span class="sxs-lookup"><span data-stu-id="714ec-255">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="714ec-256">Para obter uma implementação de exemplo fornecida pela comunidade, confira [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) (Aplicativos de página única das Páginas do GitHub) ([rafrex/spa-github-pages no GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="714ec-256">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="714ec-257">Um exemplo usando a abordagem da comunidade pode ser visto em [blazor-demo/blazor-demo.github.io no GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([site dinâmico](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="714ec-257">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="714ec-258">Ao usar um site de projeto em vez de um site de empresa, adicione ou atualize a tag `<base>` no *index.html*.</span><span class="sxs-lookup"><span data-stu-id="714ec-258">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="714ec-259">Defina o valor do atributo `<repository-name>/` do `href`, em que `<repository-name>/` é o nome do repositório do GitHub.</span><span class="sxs-lookup"><span data-stu-id="714ec-259">Set the `href` attribute value to `<repository-name>/`, where `<repository-name>/` is the GitHub repository name.</span></span>

## <a name="deploy-a-server-side-razor-components-app"></a><span data-ttu-id="714ec-260">Implantar um aplicativo dos Componentes Razor do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="714ec-260">Deploy a server-side Razor Components app</span></span>

<span data-ttu-id="714ec-261">Com o [modelo de hospedagem do lado do servidor](xref:razor-components/hosting-models#server-side-hosting-model), os Componentes Razor são executados no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="714ec-261">With the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model), Razor Components is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="714ec-262">As atualizações da interface do usuário, a manipulação de eventos e as chamadas de JavaScript são realizadas por uma conexão SignalR.</span><span class="sxs-lookup"><span data-stu-id="714ec-262">UI updates, event handling, and JavaScript calls are handled over a SignalR connection.</span></span>

<span data-ttu-id="714ec-263">O aplicativo é incluído com o aplicativo do ASP.NET Core na saída publicada para que os dois aplicativos sejam implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="714ec-263">The app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="714ec-264">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="714ec-264">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="714ec-265">Para uma implantação do lado do servidor, o Visual Studio inclui o modelo de projeto **Componentes do Razor** (modelo `razorcomponents` ao usar o comando [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="714ec-265">For a server-side deployment, Visual Studio includes the **Razor Components** project template (`razorcomponents` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="714ec-266">Quando o aplicativo do ASP.NET Core é publicado, o aplicativo dos Componentes Razor é incluído na saída publicada para que ambos possam ser implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="714ec-266">When the ASP.NET Core app is published, the Razor Components app is included in the published output so that the ASP.NET Core app and the Razor Components app can be deployed together.</span></span> <span data-ttu-id="714ec-267">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="714ec-267">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="714ec-268">Para obter informações de como implantar o Serviço de Aplicativo do Azure, confira os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="714ec-268">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>

<span data-ttu-id="714ec-269">Aprenda como publicar um aplicativo ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="714ec-269">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>
