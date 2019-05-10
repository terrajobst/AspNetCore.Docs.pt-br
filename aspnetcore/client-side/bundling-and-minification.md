---
title: Agrupar e minificar ativos estáticos no ASP.NET Core
author: scottaddie
description: Aprenda a otimizar os recursos estáticos em um aplicativo web ASP.NET Core por meio da aplicação de técnicas de agrupamento e minificação.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/20/2018
uid: client-side/bundling-and-minification
ms.openlocfilehash: 5d5f0aadb7740c9b2b959d12a585cd8c91758ce8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894293"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="cb284-103">Agrupar e minificar ativos estáticos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb284-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="cb284-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [Alcatrão de David](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="cb284-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="cb284-105">Este artigo explica os benefícios da aplicação de agrupamento e minificação, incluindo como esses recursos podem ser usados com aplicativos web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb284-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="cb284-106">O que é o agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="cb284-106">What is bundling and minification</span></span>

<span data-ttu-id="cb284-107">Agrupamento e minificação são duas otimizações de desempenho distintos, que você pode aplicar em um aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="cb284-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="cb284-108">Usados juntos, agrupamento e minificação melhoram o desempenho reduzindo o número de solicitações do servidor e reduzindo o tamanho dos ativos mais solicitados estáticos.</span><span class="sxs-lookup"><span data-stu-id="cb284-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="cb284-109">Agrupamento e minificação basicamente melhoram o tempo de carregamento de solicitação de página primeiro.</span><span class="sxs-lookup"><span data-stu-id="cb284-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="cb284-110">Depois que uma página da web foi solicitado, o navegador armazena em cache os recursos estáticos (JavaScript, CSS e imagens).</span><span class="sxs-lookup"><span data-stu-id="cb284-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="cb284-111">Consequentemente, agrupamento e minificação não melhoram o desempenho ao solicitar a mesma página ou páginas, no mesmo site que está solicitando os mesmos recursos.</span><span class="sxs-lookup"><span data-stu-id="cb284-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="cb284-112">Se a expira cabeçalho não está definido corretamente nos ativos e se não for usado o agrupamento e minificação, heurística de atualização do navegador marca os ativos obsoletos depois de alguns dias.</span><span class="sxs-lookup"><span data-stu-id="cb284-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="cb284-113">Além disso, o navegador requer uma solicitação de validação para cada ativo.</span><span class="sxs-lookup"><span data-stu-id="cb284-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="cb284-114">Nesse caso, o agrupamento e minificação fornecem uma melhoria de desempenho, mesmo após a primeira solicitação de página.</span><span class="sxs-lookup"><span data-stu-id="cb284-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="cb284-115">Agrupamento</span><span class="sxs-lookup"><span data-stu-id="cb284-115">Bundling</span></span>

<span data-ttu-id="cb284-116">O agrupamento combina vários arquivos em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="cb284-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="cb284-117">Agrupamento reduz o número de solicitações de servidor que são necessários para renderizar um ativo da web, como uma página da web.</span><span class="sxs-lookup"><span data-stu-id="cb284-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="cb284-118">Você pode criar qualquer número de pacotes individuais especificamente para o CSS, JavaScript, etc. Menos arquivos significa menos solicitações HTTP do navegador para o servidor ou do serviço de fornecimento de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb284-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="cb284-119">Isso resulta em melhor desempenho de carregamento de página primeiro.</span><span class="sxs-lookup"><span data-stu-id="cb284-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="cb284-120">Minimização</span><span class="sxs-lookup"><span data-stu-id="cb284-120">Minification</span></span>

<span data-ttu-id="cb284-121">Minificação remove caracteres desnecessários de código sem alterar a funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="cb284-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="cb284-122">O resultado é uma redução de tamanho significativo nos ativos mais solicitados (como CSS, imagens e arquivos JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cb284-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="cb284-123">Os efeitos colaterais de minimização incluem encurtar os nomes de variável para um caractere e remover comentários e espaço em branco desnecessário.</span><span class="sxs-lookup"><span data-stu-id="cb284-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="cb284-124">Considere a seguinte função de JavaScript:</span><span class="sxs-lookup"><span data-stu-id="cb284-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="cb284-125">Minimização reduz a função para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="cb284-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="cb284-126">Além de remover os comentários e espaço em branco desnecessário, os seguintes nomes de parâmetro e variável foram renomeados da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="cb284-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="cb284-127">Original</span><span class="sxs-lookup"><span data-stu-id="cb284-127">Original</span></span> | <span data-ttu-id="cb284-128">Renomeado</span><span class="sxs-lookup"><span data-stu-id="cb284-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="cb284-129">Impacto de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="cb284-129">Impact of bundling and minification</span></span>

<span data-ttu-id="cb284-130">A tabela a seguir descreve as diferenças entre carregar ativos individualmente e usando o agrupamento e minificação:</span><span class="sxs-lookup"><span data-stu-id="cb284-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="cb284-131">Ação</span><span class="sxs-lookup"><span data-stu-id="cb284-131">Action</span></span> | <span data-ttu-id="cb284-132">Com B/M</span><span class="sxs-lookup"><span data-stu-id="cb284-132">With B/M</span></span> | <span data-ttu-id="cb284-133">Sem B/M</span><span class="sxs-lookup"><span data-stu-id="cb284-133">Without B/M</span></span> | <span data-ttu-id="cb284-134">Alteração</span><span class="sxs-lookup"><span data-stu-id="cb284-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="cb284-135">Solicitações de arquivos</span><span class="sxs-lookup"><span data-stu-id="cb284-135">File Requests</span></span>  | <span data-ttu-id="cb284-136">7</span><span class="sxs-lookup"><span data-stu-id="cb284-136">7</span></span>   | <span data-ttu-id="cb284-137">18</span><span class="sxs-lookup"><span data-stu-id="cb284-137">18</span></span>     | <span data-ttu-id="cb284-138">157%</span><span class="sxs-lookup"><span data-stu-id="cb284-138">157%</span></span>
<span data-ttu-id="cb284-139">KB transferido</span><span class="sxs-lookup"><span data-stu-id="cb284-139">KB Transferred</span></span> | <span data-ttu-id="cb284-140">156</span><span class="sxs-lookup"><span data-stu-id="cb284-140">156</span></span> | <span data-ttu-id="cb284-141">264.68</span><span class="sxs-lookup"><span data-stu-id="cb284-141">264.68</span></span> | <span data-ttu-id="cb284-142">70%</span><span class="sxs-lookup"><span data-stu-id="cb284-142">70%</span></span>
<span data-ttu-id="cb284-143">Tempo de carregamento (ms)</span><span class="sxs-lookup"><span data-stu-id="cb284-143">Load Time (ms)</span></span> | <span data-ttu-id="cb284-144">885</span><span class="sxs-lookup"><span data-stu-id="cb284-144">885</span></span> | <span data-ttu-id="cb284-145">2360</span><span class="sxs-lookup"><span data-stu-id="cb284-145">2360</span></span>   | <span data-ttu-id="cb284-146">167%</span><span class="sxs-lookup"><span data-stu-id="cb284-146">167%</span></span>

<span data-ttu-id="cb284-147">Navegadores são bastante detalhados em relação a cabeçalhos de solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb284-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="cb284-148">O total de bytes enviados métrica viu uma redução significativa ao agrupamento.</span><span class="sxs-lookup"><span data-stu-id="cb284-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="cb284-149">O tempo de carregamento mostra uma melhoria significativa, no entanto, este exemplo foi executado localmente.</span><span class="sxs-lookup"><span data-stu-id="cb284-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="cb284-150">Maior ganhos de desempenho são obtidos ao usar o agrupamento e minificação com ativos transferidos por uma rede.</span><span class="sxs-lookup"><span data-stu-id="cb284-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="cb284-151">Escolher uma estratégia de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="cb284-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="cb284-152">Os modelos de projeto do MVC e páginas Razor oferecem uma solução de out-of-the-box para agrupamento e minificação consiste em um arquivo de configuração JSON.</span><span class="sxs-lookup"><span data-stu-id="cb284-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="cb284-153">Ferramentas de terceiros, tais como o [Gulp](xref:client-side/using-gulp) e [Grunt](xref:client-side/using-grunt) executores de tarefas, executar as mesmas tarefas com um pouco mais complexidade.</span><span class="sxs-lookup"><span data-stu-id="cb284-153">Third-party tools, such as the [Gulp](xref:client-side/using-gulp) and [Grunt](xref:client-side/using-grunt) task runners, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="cb284-154">Uma ferramenta de terceiros é uma excelente opção quando o fluxo de trabalho de desenvolvimento requer processamento além do agrupamento e minificação&mdash;como otimização linting e imagem.</span><span class="sxs-lookup"><span data-stu-id="cb284-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="cb284-155">Usando o agrupamento e minificação tempo de design, os arquivos reduzidos são criados antes da implantação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb284-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="cb284-156">Empacotando e minimizando antes da implantação tem a vantagem de carga do servidor reduzido.</span><span class="sxs-lookup"><span data-stu-id="cb284-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="cb284-157">No entanto, é importante reconhecer que esse tempo de design de agrupamento e minificação aumenta a complexidade de build e só funciona com arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="cb284-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="cb284-158">Configurar o agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="cb284-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="cb284-159">No ASP.NET Core 2.0 ou anterior, os modelos de projeto do MVC e páginas do Razor fornecem uma *bundleconfig.json* arquivo de configuração que define as opções para cada pacote:</span><span class="sxs-lookup"><span data-stu-id="cb284-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="cb284-160">No ASP.NET Core 2.1 ou posterior, adicione um novo arquivo JSON, denominado *bundleconfig.json*, para a raiz do projeto MVC ou páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="cb284-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="cb284-161">Inclua o JSON a seguir no arquivo como um ponto de partida:</span><span class="sxs-lookup"><span data-stu-id="cb284-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="cb284-162">O *bundleconfig.json* arquivo define as opções para cada pacote.</span><span class="sxs-lookup"><span data-stu-id="cb284-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="cb284-163">No exemplo anterior, uma configuração de pacote único é definida para o JavaScript personalizado (*wwwroot/js/site.js*) e a folha de estilos (*wwwroot/css/site.css*) arquivos.</span><span class="sxs-lookup"><span data-stu-id="cb284-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="cb284-164">Opções de configuração incluem:</span><span class="sxs-lookup"><span data-stu-id="cb284-164">Configuration options include:</span></span>

* <span data-ttu-id="cb284-165">`outputFileName`: O nome do arquivo de pacote de saída.</span><span class="sxs-lookup"><span data-stu-id="cb284-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="cb284-166">Pode conter um caminho relativo do *bundleconfig.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="cb284-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="cb284-167">**required**</span><span class="sxs-lookup"><span data-stu-id="cb284-167">**required**</span></span>
* <span data-ttu-id="cb284-168">`inputFiles`: Uma matriz de arquivos para agrupar.</span><span class="sxs-lookup"><span data-stu-id="cb284-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="cb284-169">Esses são os caminhos relativos para o arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="cb284-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="cb284-170">**opcional**, \* um valor vazio resulta em um arquivo de saída vazia.</span><span class="sxs-lookup"><span data-stu-id="cb284-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="cb284-171">[recurso de curinga](http://www.tldp.org/LDP/abs/html/globbingref.html) padrões são suportados.</span><span class="sxs-lookup"><span data-stu-id="cb284-171">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="cb284-172">`minify`: As opções de minimização para o tipo de saída.</span><span class="sxs-lookup"><span data-stu-id="cb284-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="cb284-173">**optional**, *default - `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="cb284-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="cb284-174">Opções de configuração estão disponíveis por tipo de arquivo de saída.</span><span class="sxs-lookup"><span data-stu-id="cb284-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="cb284-175">Minificador CSS</span><span class="sxs-lookup"><span data-stu-id="cb284-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="cb284-176">Minificador de JavaScript</span><span class="sxs-lookup"><span data-stu-id="cb284-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="cb284-177">Minificador de HTML</span><span class="sxs-lookup"><span data-stu-id="cb284-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="cb284-178">`includeInProject`: Sinalizador que indica se deseja adicionar os arquivos gerados para o arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="cb284-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="cb284-179">**opcional**, *padrão – false*</span><span class="sxs-lookup"><span data-stu-id="cb284-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="cb284-180">`sourceMap`: Sinalizador que indica se é necessário gerar um mapa de código-fonte para o arquivo agrupado.</span><span class="sxs-lookup"><span data-stu-id="cb284-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="cb284-181">**opcional**, *padrão – false*</span><span class="sxs-lookup"><span data-stu-id="cb284-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="cb284-182">`sourceMapRootPath`: O caminho raiz para armazenar o arquivo de mapa de código-fonte gerado.</span><span class="sxs-lookup"><span data-stu-id="cb284-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="cb284-183">Compilação em tempo de execução de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="cb284-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="cb284-184">O [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) pacote NuGet permite que a execução de agrupamento e minificação no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="cb284-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="cb284-185">O pacote injeta [destinos do MSBuild](/visualstudio/msbuild/msbuild-targets) quais executar na compilação e de hora limpa.</span><span class="sxs-lookup"><span data-stu-id="cb284-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="cb284-186">O *bundleconfig.json* arquivo é analisado pelo processo de compilação para produzir os arquivos de saída com base na configuração definida.</span><span class="sxs-lookup"><span data-stu-id="cb284-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="cb284-187">BuildBundlerMinifier pertence a um projeto voltado à comunidade no GitHub para o qual a Microsoft fornece sem suporte.</span><span class="sxs-lookup"><span data-stu-id="cb284-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="cb284-188">Problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="cb284-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cb284-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb284-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb284-190">Adicione a *BuildBundlerMinifier* pacote ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="cb284-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="cb284-191">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="cb284-191">Build the project.</span></span> <span data-ttu-id="cb284-192">O exemplo a seguir é exibida na janela de saída:</span><span class="sxs-lookup"><span data-stu-id="cb284-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="cb284-193">Limpe o projeto.</span><span class="sxs-lookup"><span data-stu-id="cb284-193">Clean the project.</span></span> <span data-ttu-id="cb284-194">O exemplo a seguir é exibida na janela de saída:</span><span class="sxs-lookup"><span data-stu-id="cb284-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cb284-195">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="cb284-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb284-196">Adicione a *BuildBundlerMinifier* pacote ao seu projeto:</span><span class="sxs-lookup"><span data-stu-id="cb284-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="cb284-197">Se usar o ASP.NET Core 1.x, restaure o pacote recém adicionado:</span><span class="sxs-lookup"><span data-stu-id="cb284-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```console
dotnet restore
```

<span data-ttu-id="cb284-198">Compile o projeto:</span><span class="sxs-lookup"><span data-stu-id="cb284-198">Build the project:</span></span>

```console
dotnet build
```

<span data-ttu-id="cb284-199">O exemplo a seguir será exibida:</span><span class="sxs-lookup"><span data-stu-id="cb284-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="cb284-200">Limpe o projeto:</span><span class="sxs-lookup"><span data-stu-id="cb284-200">Clean the project:</span></span>

```console
dotnet clean
```

<span data-ttu-id="cb284-201">A saída a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="cb284-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="cb284-202">Execução ad hoc de agrupamento e minificação</span><span class="sxs-lookup"><span data-stu-id="cb284-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="cb284-203">É possível executar as tarefas de empacotamento e minimização em uma base ad hoc, sem compilar o projeto.</span><span class="sxs-lookup"><span data-stu-id="cb284-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="cb284-204">Adicione a [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) pacote NuGet ao seu projeto:</span><span class="sxs-lookup"><span data-stu-id="cb284-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="cb284-205">BundlerMinifier.Core pertence a um projeto voltado à comunidade no GitHub para o qual a Microsoft fornece sem suporte.</span><span class="sxs-lookup"><span data-stu-id="cb284-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="cb284-206">Problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="cb284-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="cb284-207">Este pacote estende a CLI do .NET Core para incluir a *pacote dotnet* ferramenta.</span><span class="sxs-lookup"><span data-stu-id="cb284-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="cb284-208">O comando a seguir pode ser executado na janela do Console de Gerenciador de pacote (PMC) ou em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="cb284-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```console
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="cb284-209">Gerenciador de pacotes NuGet adiciona as dependências para o arquivo \*. csproj como `<PackageReference />` nós.</span><span class="sxs-lookup"><span data-stu-id="cb284-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="cb284-210">O `dotnet bundle` comando está registrado com a CLI do .NET Core somente quando um `<DotNetCliToolReference />` nó é usado.</span><span class="sxs-lookup"><span data-stu-id="cb284-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="cb284-211">Modifique o arquivo \*. csproj adequadamente.</span><span class="sxs-lookup"><span data-stu-id="cb284-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="cb284-212">Adicionar arquivos ao fluxo de trabalho</span><span class="sxs-lookup"><span data-stu-id="cb284-212">Add files to workflow</span></span>

<span data-ttu-id="cb284-213">Considere um exemplo no qual adicional *Custom. CSS* arquivo for adicionado a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb284-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="cb284-214">Para minificar *Custom. CSS* e agrupá-la com *CSS* em um *site* arquivo, adicione o caminho relativo para *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="cb284-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="cb284-215">Como alternativa, é possível usar o seguinte padrão de recurso de curinga:</span><span class="sxs-lookup"><span data-stu-id="cb284-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> <span data-ttu-id="cb284-216">Esse padrão de recurso de curinga corresponde a todos os arquivos CSS e exclui o padrão de arquivo reduzido.</span><span class="sxs-lookup"><span data-stu-id="cb284-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="cb284-217">Compile o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb284-217">Build the application.</span></span> <span data-ttu-id="cb284-218">Abra *site* e observe o conteúdo da *Custom. CSS* é acrescentado ao final do arquivo.</span><span class="sxs-lookup"><span data-stu-id="cb284-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="cb284-219">Agrupamento e minificação com base no ambiente</span><span class="sxs-lookup"><span data-stu-id="cb284-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="cb284-220">Como prática recomendada, os arquivos agrupados e minificados do seu aplicativo devem ser usados em um ambiente de produção.</span><span class="sxs-lookup"><span data-stu-id="cb284-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="cb284-221">Durante o desenvolvimento, os arquivos originais fazem para depuração mais fácil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb284-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="cb284-222">Especificar quais arquivos serão incluídos em suas páginas usando o [auxiliar de marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) em modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="cb284-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="cb284-223">O auxiliar de marca de ambiente renderiza apenas seu conteúdo ao executar em particular [ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="cb284-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="cb284-224">O seguinte `environment` marca renderiza os arquivos CSS não processados durante a execução no `Development` ambiente:</span><span class="sxs-lookup"><span data-stu-id="cb284-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="cb284-225">O seguinte `environment` marca renderiza os arquivos CSS agrupados e minificados quando em execução em um ambiente diferente de `Development`.</span><span class="sxs-lookup"><span data-stu-id="cb284-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="cb284-226">Por exemplo, em execução no `Production` ou `Staging` dispara o processamento dessas folhas de estilo:</span><span class="sxs-lookup"><span data-stu-id="cb284-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="cb284-227">Consumir bundleconfig.json do Gulp</span><span class="sxs-lookup"><span data-stu-id="cb284-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="cb284-228">Há casos em que o fluxo de trabalho agrupamento e minificação do aplicativo requer processamento adicional.</span><span class="sxs-lookup"><span data-stu-id="cb284-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="cb284-229">Exemplos incluem processamento de ativos da CDN, extrapolação de cache e otimização de imagem.</span><span class="sxs-lookup"><span data-stu-id="cb284-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="cb284-230">Para atender a esses requisitos, você pode converter o fluxo de trabalho de agrupamento e minificação para usar o Gulp.</span><span class="sxs-lookup"><span data-stu-id="cb284-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="cb284-231">Usar a extensão Bundler & Minifier</span><span class="sxs-lookup"><span data-stu-id="cb284-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="cb284-232">O Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extensão lida com a conversão para o Gulp.</span><span class="sxs-lookup"><span data-stu-id="cb284-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="cb284-233">A extensão Bundler & Minifier pertence a um projeto voltado à comunidade no GitHub para o qual a Microsoft fornece sem suporte.</span><span class="sxs-lookup"><span data-stu-id="cb284-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="cb284-234">Problemas devem ser arquivados [aqui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="cb284-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="cb284-235">Clique com botão direito do *bundleconfig.json* arquivo no Gerenciador de soluções e selecione **Bundler & Minifier** > **converter Gulp...** :</span><span class="sxs-lookup"><span data-stu-id="cb284-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Converter para Gulp item de menu de contexto](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="cb284-237">O *gulpfile. js* e *Package. JSON* arquivos são adicionados ao projeto.</span><span class="sxs-lookup"><span data-stu-id="cb284-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="cb284-238">O suporte a [npm](https://www.npmjs.com/) os pacotes listados na *Package. JSON* do arquivo `devDependencies` seção estão instalados.</span><span class="sxs-lookup"><span data-stu-id="cb284-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="cb284-239">Execute o seguinte comando na janela do PMC para instalar a CLI do Gulp como uma dependência global:</span><span class="sxs-lookup"><span data-stu-id="cb284-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="cb284-240">O *gulpfile. js* leituras de arquivos a *bundleconfig.json* arquivo para as entradas, saídas e as configurações.</span><span class="sxs-lookup"><span data-stu-id="cb284-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="cb284-241">Converter manualmente</span><span class="sxs-lookup"><span data-stu-id="cb284-241">Convert manually</span></span>

<span data-ttu-id="cb284-242">Se o Visual Studio e/ou a extensão Bundler & Minifier não estiverem disponível, converta manualmente.</span><span class="sxs-lookup"><span data-stu-id="cb284-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="cb284-243">Adicionar um *Package. JSON* arquivo pelo seguinte `devDependencies`, para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="cb284-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="cb284-244">Instalar as dependências, executando o comando a seguir no mesmo nível que *Package. JSON*:</span><span class="sxs-lookup"><span data-stu-id="cb284-244">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="cb284-245">Instale a CLI do Gulp como uma dependência global:</span><span class="sxs-lookup"><span data-stu-id="cb284-245">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="cb284-246">Cópia de *gulpfile. js* abaixo o arquivo para a raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="cb284-246">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="cb284-247">Executar tarefas de Gulp</span><span class="sxs-lookup"><span data-stu-id="cb284-247">Run Gulp tasks</span></span>

<span data-ttu-id="cb284-248">Para disparar a tarefa do Gulp minificação antes que o projeto é compilado no Visual Studio, adicione o seguinte [destino do MSBuild](/visualstudio/msbuild/msbuild-targets) para o arquivo \*. csproj:</span><span class="sxs-lookup"><span data-stu-id="cb284-248">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="cb284-249">Neste exemplo, as tarefas definidas dentro de `MyPreCompileTarget` execução antes do predefinidos de destino `Build` destino.</span><span class="sxs-lookup"><span data-stu-id="cb284-249">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="cb284-250">Saída semelhante à seguinte é exibida na janela de saída do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="cb284-250">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

<span data-ttu-id="cb284-251">Como alternativa, o Gerenciador de executor de tarefas do Visual Studio pode ser usado para associar as tarefas de Gulp a eventos específicos do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cb284-251">Alternatively, Visual Studio's Task Runner Explorer may be used to bind Gulp tasks to specific Visual Studio events.</span></span> <span data-ttu-id="cb284-252">Ver [execução de tarefas padrão](xref:client-side/using-gulp#running-default-tasks) para obter instruções sobre como fazer isso.</span><span class="sxs-lookup"><span data-stu-id="cb284-252">See [Running default tasks](xref:client-side/using-gulp#running-default-tasks) for instructions on doing that.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb284-253">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cb284-253">Additional resources</span></span>

* [<span data-ttu-id="cb284-254">Usar o Gulp</span><span class="sxs-lookup"><span data-stu-id="cb284-254">Use Gulp</span></span>](xref:client-side/using-gulp)
* [<span data-ttu-id="cb284-255">Usar o Grunt</span><span class="sxs-lookup"><span data-stu-id="cb284-255">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="cb284-256">Usar vários ambientes</span><span class="sxs-lookup"><span data-stu-id="cb284-256">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="cb284-257">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="cb284-257">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
