---
title: Empacotar e reduzir ativos estáticos no ASP.NET Core
author: scottaddie
description: Saiba como otimizar recursos estáticos em um aplicativo Web ASP.NET Core aplicando técnicas de agrupamento e minificação.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803273"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="4a2d8-103">Empacotar e reduzir ativos estáticos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4a2d8-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="4a2d8-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [David Pinheiro](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="4a2d8-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="4a2d8-105">Este artigo explica os benefícios da aplicação de agrupamento e minificação, incluindo como esses recursos podem ser usados com ASP.NET Core aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="4a2d8-106">O que é agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-106">What is bundling and minification</span></span>

<span data-ttu-id="4a2d8-107">O agrupamento e o minificação são duas otimizações de desempenho distintas que você pode aplicar em um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="4a2d8-108">Usados juntos, o agrupamento e o minificação melhoram o desempenho, reduzindo o número de solicitações do servidor e reduzindo o tamanho dos ativos estáticos solicitados.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="4a2d8-109">O agrupamento e o minificação melhoram principalmente o tempo de carregamento da solicitação da primeira página.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="4a2d8-110">Depois que uma página da Web for solicitada, o navegador armazenará em cache os ativos estáticos (JavaScript, CSS e imagens).</span><span class="sxs-lookup"><span data-stu-id="4a2d8-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="4a2d8-111">Consequentemente, o agrupamento e o minificação não melhoram o desempenho ao solicitar a mesma página, ou páginas, no mesmo site que solicita os mesmos ativos.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="4a2d8-112">Se o cabeçalho Expires não estiver definido corretamente nos ativos e se o agrupamento e o minificação não forem usados, a heurística de atualização do navegador marcará os ativos como obsoletos após alguns dias.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="4a2d8-113">Além disso, o navegador requer uma solicitação de validação para cada ativo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="4a2d8-114">Nesse caso, o agrupamento e o minificação fornecem uma melhoria de desempenho mesmo após a primeira solicitação de página.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="4a2d8-115">Reagrupamento</span><span class="sxs-lookup"><span data-stu-id="4a2d8-115">Bundling</span></span>

<span data-ttu-id="4a2d8-116">O agrupamento combina vários arquivos em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="4a2d8-117">O agrupamento reduz o número de solicitações de servidor necessárias para processar um ativo da Web, como uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="4a2d8-118">Você pode criar qualquer número de grupos individuais especificamente para CSS, JavaScript, etc. Menos arquivos significa menos solicitações HTTP do navegador para o servidor ou do serviço que fornece seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="4a2d8-119">Isso resulta em um melhor desempenho de carregamento de página.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="4a2d8-120">Minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-120">Minification</span></span>

<span data-ttu-id="4a2d8-121">Minificação remove caracteres desnecessários do código sem alterar a funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="4a2d8-122">O resultado é uma redução significativa de tamanho em ativos solicitados (como CSS, imagens e arquivos JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4a2d8-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="4a2d8-123">Os efeitos colaterais comuns de minificação incluem a redução de nomes de variáveis para um caractere e a remoção de comentários e espaço em branco desnecessário.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="4a2d8-124">Considere a seguinte função JavaScript:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="4a2d8-125">Minificação reduz a função para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="4a2d8-126">Além de remover os comentários e o espaço em branco desnecessário, os seguintes parâmetros e nomes de variáveis foram renomeados da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="4a2d8-127">Original</span><span class="sxs-lookup"><span data-stu-id="4a2d8-127">Original</span></span> | <span data-ttu-id="4a2d8-128">Renomeado</span><span class="sxs-lookup"><span data-stu-id="4a2d8-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="4a2d8-129">Impacto do agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-129">Impact of bundling and minification</span></span>

<span data-ttu-id="4a2d8-130">A tabela a seguir descreve as diferenças entre os ativos de carregamento individualmente e o uso de agrupamento e minificação:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="4a2d8-131">Action</span><span class="sxs-lookup"><span data-stu-id="4a2d8-131">Action</span></span> | <span data-ttu-id="4a2d8-132">Com B/M</span><span class="sxs-lookup"><span data-stu-id="4a2d8-132">With B/M</span></span> | <span data-ttu-id="4a2d8-133">Sem B/M</span><span class="sxs-lookup"><span data-stu-id="4a2d8-133">Without B/M</span></span> | <span data-ttu-id="4a2d8-134">Alteração</span><span class="sxs-lookup"><span data-stu-id="4a2d8-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="4a2d8-135">Solicitações de arquivo</span><span class="sxs-lookup"><span data-stu-id="4a2d8-135">File Requests</span></span>  | <span data-ttu-id="4a2d8-136">7</span><span class="sxs-lookup"><span data-stu-id="4a2d8-136">7</span></span>   | <span data-ttu-id="4a2d8-137">18</span><span class="sxs-lookup"><span data-stu-id="4a2d8-137">18</span></span>     | <span data-ttu-id="4a2d8-138">157%</span><span class="sxs-lookup"><span data-stu-id="4a2d8-138">157%</span></span>
<span data-ttu-id="4a2d8-139">KB transferidos</span><span class="sxs-lookup"><span data-stu-id="4a2d8-139">KB Transferred</span></span> | <span data-ttu-id="4a2d8-140">156</span><span class="sxs-lookup"><span data-stu-id="4a2d8-140">156</span></span> | <span data-ttu-id="4a2d8-141">264.68</span><span class="sxs-lookup"><span data-stu-id="4a2d8-141">264.68</span></span> | <span data-ttu-id="4a2d8-142">70%</span><span class="sxs-lookup"><span data-stu-id="4a2d8-142">70%</span></span>
<span data-ttu-id="4a2d8-143">Tempo de carregamento (MS)</span><span class="sxs-lookup"><span data-stu-id="4a2d8-143">Load Time (ms)</span></span> | <span data-ttu-id="4a2d8-144">885</span><span class="sxs-lookup"><span data-stu-id="4a2d8-144">885</span></span> | <span data-ttu-id="4a2d8-145">2360</span><span class="sxs-lookup"><span data-stu-id="4a2d8-145">2360</span></span>   | <span data-ttu-id="4a2d8-146">167%</span><span class="sxs-lookup"><span data-stu-id="4a2d8-146">167%</span></span>

<span data-ttu-id="4a2d8-147">Os navegadores são bastante detalhados em relação aos cabeçalhos de solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="4a2d8-148">A métrica total de bytes enviados observou uma redução significativa durante o agrupamento.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="4a2d8-149">O tempo de carregamento mostra uma melhoria significativa, no entanto, este exemplo foi executado localmente.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="4a2d8-150">São obtidos maiores ganhos de desempenho ao usar agrupamento e minificação com ativos transferidos por uma rede.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="4a2d8-151">Escolher uma estratégia de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="4a2d8-152">Os modelos de projeto MVC e Razor Pages fornecem uma solução pronta para uso para Agrupamento e minificação consistentes em um arquivo de configuração JSON.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="4a2d8-153">Ferramentas de terceiros, como o executor de tarefas do [Grunt](xref:client-side/using-grunt) , realizam as mesmas tarefas com um pouco mais de complexidade.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="4a2d8-154">Uma ferramenta de terceiros é uma ótima opção quando seu fluxo de trabalho de desenvolvimento requer processamento além do agrupamento e minificação&mdash;como a retratação e a otimização de imagem.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="4a2d8-155">Usando agrupamento e minificação de tempo de design, os arquivos reduzidos são criados antes da implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="4a2d8-156">O agrupamento e o minificar antes da implantação proporcionam a vantagem da carga reduzida do servidor.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="4a2d8-157">No entanto, é importante reconhecer que o agrupamento de tempo de design e minificação aumenta a complexidade da compilação e só funciona com arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="4a2d8-158">Configurar agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="4a2d8-159">No ASP.NET Core 2,0 ou anterior, os modelos de projeto MVC e Razor Pages fornecem um arquivo de configuração *bundleconfig. JSON* que define as opções para cada pacote:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4a2d8-160">No ASP.NET Core 2,1 ou posterior, adicione um novo arquivo JSON, chamado *bundleconfig. JSON*, à raiz do projeto MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="4a2d8-161">Inclua o seguinte JSON nesse arquivo como um ponto de partida:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="4a2d8-162">O arquivo *bundleconfig. JSON* define as opções para cada pacote.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="4a2d8-163">No exemplo anterior, uma única configuração de pacote é definida para os arquivos JavaScript personalizado (*wwwroot/js/site. js*) e folha de estilos (*wwwroot/CSS/site. css*).</span><span class="sxs-lookup"><span data-stu-id="4a2d8-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="4a2d8-164">As opções de configuração incluem:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-164">Configuration options include:</span></span>

* <span data-ttu-id="4a2d8-165">`outputFileName`: o nome do arquivo de pacote a ser impresso.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="4a2d8-166">Pode conter um caminho relativo do arquivo *bundleconfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4a2d8-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="4a2d8-167">**obrigatório**</span><span class="sxs-lookup"><span data-stu-id="4a2d8-167">**required**</span></span>
* <span data-ttu-id="4a2d8-168">`inputFiles`: uma matriz de arquivos para agrupar.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="4a2d8-169">Esses são caminhos relativos ao arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="4a2d8-170">**opcional**, \* um valor vazio resulta em um arquivo de saída vazio.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="4a2d8-171">Há suporte para padrões de [mascaramento](https://www.tldp.org/LDP/abs/html/globbingref.html) .</span><span class="sxs-lookup"><span data-stu-id="4a2d8-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="4a2d8-172">`minify`: as opções de minificação para o tipo de saída.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="4a2d8-173">**opcional**, *padrão-`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="4a2d8-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="4a2d8-174">As opções de configuração estão disponíveis por tipo de arquivo de saída.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="4a2d8-175">Minificador CSS</span><span class="sxs-lookup"><span data-stu-id="4a2d8-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="4a2d8-176">Minificador JavaScript</span><span class="sxs-lookup"><span data-stu-id="4a2d8-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="4a2d8-177">Minificador HTML</span><span class="sxs-lookup"><span data-stu-id="4a2d8-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="4a2d8-178">`includeInProject`: sinalizador que indica se os arquivos gerados devem ser adicionados ao arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="4a2d8-179">**opcional**, *padrão-falso*</span><span class="sxs-lookup"><span data-stu-id="4a2d8-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="4a2d8-180">`sourceMap`: sinalizador que indica se um mapa de origem deve ser gerado para o arquivo agrupado.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="4a2d8-181">**opcional**, *padrão-falso*</span><span class="sxs-lookup"><span data-stu-id="4a2d8-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="4a2d8-182">`sourceMapRootPath`: o caminho raiz para armazenar o arquivo de mapa de origem gerado.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="4a2d8-183">Execução em tempo de compilação de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="4a2d8-184">O pacote NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) permite a execução de agrupamento e minificação no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="4a2d8-185">O pacote injeta [destinos do MSBuild](/visualstudio/msbuild/msbuild-targets) que são executados em tempo de compilação e limpeza.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="4a2d8-186">O arquivo *bundleconfig. JSON* é analisado pelo processo de compilação para produzir os arquivos de saída com base na configuração definida.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="4a2d8-187">O BuildBundlerMinifier pertence a um projeto controlado pela Comunidade no GitHub para o qual a Microsoft não oferece nenhum suporte.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="4a2d8-188">Os problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="4a2d8-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4a2d8-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a2d8-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4a2d8-190">Adicione o pacote *BuildBundlerMinifier* ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="4a2d8-191">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-191">Build the project.</span></span> <span data-ttu-id="4a2d8-192">O seguinte aparece na janela de saída:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-192">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="4a2d8-193">Limpe o projeto.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-193">Clean the project.</span></span> <span data-ttu-id="4a2d8-194">O seguinte aparece na janela de saída:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4a2d8-195">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4a2d8-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4a2d8-196">Adicione o pacote *BuildBundlerMinifier* ao seu projeto:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="4a2d8-197">Se estiver usando ASP.NET Core 1. x, restaure o pacote recém-adicionado:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="4a2d8-198">Compile o projeto:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="4a2d8-199">O seguinte é exibido:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="4a2d8-200">Limpe o projeto:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="4a2d8-201">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="4a2d8-202">Execução ad hoc de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="4a2d8-203">É possível executar as tarefas de agrupamento e minificação em uma base ad hoc, sem compilar o projeto.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="4a2d8-204">Adicione o pacote NuGet [BundlerMinifier. Core](https://www.nuget.org/packages/BundlerMinifier.Core/) ao seu projeto:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="4a2d8-205">O BundlerMinifier. Core pertence a um projeto controlado pela Comunidade no GitHub para o qual a Microsoft não oferece nenhum suporte.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="4a2d8-206">Os problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="4a2d8-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="4a2d8-207">Esse pacote estende a CLI do .NET Core para incluir a ferramenta *dotnet-Bundle* .</span><span class="sxs-lookup"><span data-stu-id="4a2d8-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="4a2d8-208">O comando a seguir pode ser executado na janela do console do Gerenciador de pacotes (PMC) ou em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="4a2d8-209">O Gerenciador de pacotes NuGet adiciona dependências ao arquivo \*. csproj como nós `<PackageReference />`.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="4a2d8-210">O comando `dotnet bundle` é registrado com o CLI do .NET Core somente quando um nó `<DotNetCliToolReference />` é usado.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="4a2d8-211">Modifique o arquivo \*. csproj de acordo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="4a2d8-212">Adicionar arquivos ao fluxo de trabalho</span><span class="sxs-lookup"><span data-stu-id="4a2d8-212">Add files to workflow</span></span>

<span data-ttu-id="4a2d8-213">Considere um exemplo no qual um arquivo *. CSS personalizado* adicional é adicionado semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="4a2d8-214">Para reduzir *Custom. css* e agrupá-lo com *site. css* em um arquivo *site. min. css* , adicione o caminho relativo a *bundleconfig. JSON*:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="4a2d8-215">Como alternativa, o seguinte padrão de mascaramento poderia ser usado:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="4a2d8-216">Esse padrão de mascaramento corresponde a todos os arquivos CSS e exclui o padrão de arquivo reduzidos.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="4a2d8-217">Compile o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-217">Build the application.</span></span> <span data-ttu-id="4a2d8-218">Abra *site. min. css* e observe que o conteúdo de *Custom. css* é acrescentado ao final do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="4a2d8-219">Agrupamento baseado em ambiente e minificação</span><span class="sxs-lookup"><span data-stu-id="4a2d8-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="4a2d8-220">Como prática recomendada, os arquivos agrupados e reduzidos do seu aplicativo devem ser usados em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="4a2d8-221">Durante o desenvolvimento, os arquivos originais tornam a depuração mais fácil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="4a2d8-222">Especifique quais arquivos incluir em suas páginas usando o auxiliar de [marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) em suas exibições.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="4a2d8-223">O auxiliar de marca de ambiente só renderiza seu conteúdo quando executado em [ambientes](xref:fundamentals/environments)específicos.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4a2d8-224">A marca de `environment` a seguir renderiza os arquivos CSS não processados durante a execução no ambiente de `Development`:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="4a2d8-225">A marca de `environment` a seguir renderiza os arquivos CSS agrupados e reduzidos quando executados em um ambiente diferente de `Development`.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="4a2d8-226">Por exemplo, a execução em `Production` ou `Staging` aciona a renderização dessas folhas de estilo:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="4a2d8-227">Consumir bundleconfig. JSON de Gulp</span><span class="sxs-lookup"><span data-stu-id="4a2d8-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="4a2d8-228">Há casos em que o fluxo de trabalho de agrupamento e minificação de um aplicativo requer processamento adicional.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="4a2d8-229">Os exemplos incluem otimização de imagem, de cache e processamento de ativos CDN.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="4a2d8-230">Para atender a esses requisitos, você pode converter o agrupamento e o fluxo de trabalho minificação para usar o Gulp.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="4a2d8-231">Usar a extensão de & Minificador do pacote</span><span class="sxs-lookup"><span data-stu-id="4a2d8-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="4a2d8-232">O pacote do Visual Studio [& extensão minificador](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) manipula a conversão em Gulp.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="4a2d8-233">O pacote & extensão Minificador pertence a um projeto controlado pela Comunidade no GitHub para o qual a Microsoft não oferece suporte.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="4a2d8-234">Os problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="4a2d8-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="4a2d8-235">Clique com o botão direito do mouse no arquivo *bundleconfig. JSON* em Gerenciador de soluções e selecione **pacote & Minificador** > **converter em Gulp...** :</span><span class="sxs-lookup"><span data-stu-id="4a2d8-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Converter em item de menu de contexto Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="4a2d8-237">Os arquivos *gulpfile. js* e *Package. JSON* são adicionados ao projeto.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="4a2d8-238">Os pacotes [NPM](https://www.npmjs.com/) de suporte listados na seção `devDependencies` do arquivo *Package. JSON* são instalados.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="4a2d8-239">Execute o seguinte comando na janela PMC para instalar a CLI do Gulp como uma dependência global:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="4a2d8-240">O arquivo *gulpfile. js* lê o arquivo *bundleconfig. JSON* para as entradas, saídas e configurações.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="4a2d8-241">Converter manualmente</span><span class="sxs-lookup"><span data-stu-id="4a2d8-241">Convert manually</span></span>

<span data-ttu-id="4a2d8-242">Se o Visual Studio e/ou o pacote & extensão Minificador não estiver disponível, converta manualmente.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="4a2d8-243">Adicione um arquivo *Package. JSON* , com o seguinte `devDependencies`, à raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="4a2d8-244">O módulo `gulp-uglify` não dá suporte a ECMAScript (ES) 2015/ES6 e posterior.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="4a2d8-245">Instale o [Gulp-terser](https://www.npmjs.com/package/gulp-terser) em vez de `gulp-uglify` para usar o ES2015/ES6 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="4a2d8-246">Instale as dependências executando o seguinte comando no mesmo nível que *Package. JSON*:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="4a2d8-247">Instale a CLI do Gulp como uma dependência global:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="4a2d8-248">Copie o arquivo *gulpfile. js* abaixo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="4a2d8-249">Executar tarefas do Gulp</span><span class="sxs-lookup"><span data-stu-id="4a2d8-249">Run Gulp tasks</span></span>

<span data-ttu-id="4a2d8-250">Para disparar a tarefa Gulp minificação antes que o projeto seja compilado no Visual Studio, adicione o seguinte [destino do MSBuild](/visualstudio/msbuild/msbuild-targets) ao arquivo \*. csproj:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="4a2d8-251">Neste exemplo, todas as tarefas definidas dentro do destino de `MyPreCompileTarget` são executadas antes do destino de `Build` predefinido.</span><span class="sxs-lookup"><span data-stu-id="4a2d8-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="4a2d8-252">Uma saída semelhante à seguinte aparece na janela de saída do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4a2d8-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="4a2d8-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4a2d8-253">Additional resources</span></span>

* [<span data-ttu-id="4a2d8-254">Usar o Grunt</span><span class="sxs-lookup"><span data-stu-id="4a2d8-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="4a2d8-255">Usar vários ambientes</span><span class="sxs-lookup"><span data-stu-id="4a2d8-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="4a2d8-256">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="4a2d8-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
