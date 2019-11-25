---
title: Usar ASP.NET Core SignalR com TypeScript e webpack
author: ssougnez
description: Neste tutorial, você configura o webpack para agrupar e criar um ASP.NET Core SignalR aplicativo Web cujo cliente é escrito em TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: a7c99c9e79647995886aec5b3a91584fd2f24451
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317480"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="dafbc-103">Usar ASP.NET Core SignalR com TypeScript e webpack</span><span class="sxs-lookup"><span data-stu-id="dafbc-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="dafbc-104">Por [Sébastien Sougnez](https://twitter.com/ssougnez) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="dafbc-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="dafbc-105">O [Webpack](https://webpack.js.org/) habilita os desenvolvedores a agrupar e criar recursos de um aplicativo Web do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="dafbc-106">Este tutorial demonstra como usar o webpack em um ASP.NET Core SignalR aplicativo Web cujo cliente é escrito em [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="dafbc-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="dafbc-107">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="dafbc-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dafbc-108">Scaffold um aplicativo de SignalR inicial ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dafbc-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="dafbc-109">Configurar o cliente TypeScript SignalR</span><span class="sxs-lookup"><span data-stu-id="dafbc-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="dafbc-110">Configurar um pipeline de build usando o Webpack</span><span class="sxs-lookup"><span data-stu-id="dafbc-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="dafbc-111">Configurar o servidor de SignalR</span><span class="sxs-lookup"><span data-stu-id="dafbc-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="dafbc-112">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="dafbc-112">Enable communication between client and server</span></span>

<span data-ttu-id="dafbc-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dafbc-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="dafbc-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="dafbc-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafbc-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafbc-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dafbc-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**</span><span class="sxs-lookup"><span data-stu-id="dafbc-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="dafbc-117">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dafbc-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="dafbc-118">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="dafbc-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafbc-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="dafbc-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="dafbc-121">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dafbc-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="dafbc-122">C# para Visual Studio Code versão 1.17.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dafbc-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="dafbc-123">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="dafbc-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="dafbc-124">Criar o aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dafbc-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafbc-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafbc-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafbc-126">Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="dafbc-127">Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação.</span><span class="sxs-lookup"><span data-stu-id="dafbc-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="dafbc-128">Siga estas instruções no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dafbc-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="dafbc-129">Navegue até **ferramentas** > **Opções** > **projetos e soluções** > **Web gerenciamento de pacotes** > **ferramentas da Web externas**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="dafbc-130">Selecione a entrada *$(PATH)* na lista.</span><span class="sxs-lookup"><span data-stu-id="dafbc-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="dafbc-131">Clique na seta para cima para mover a entrada para a segunda posição da lista.</span><span class="sxs-lookup"><span data-stu-id="dafbc-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="dafbc-133">A configuração do Visual Studio foi concluída.</span><span class="sxs-lookup"><span data-stu-id="dafbc-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="dafbc-134">É hora de criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-134">It's time to create the project.</span></span>

1. <span data-ttu-id="dafbc-135">Use a opção de menu **arquivo** > **nova** > **projeto** e escolha o modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="dafbc-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="dafbc-136">Nomeie o projeto *SignalRWebPack*e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="dafbc-137">Selecione *.NET Core* na lista suspensa estrutura de destino e selecione *ASP.NET Core 3,0* na lista suspensa seletor de estrutura.</span><span class="sxs-lookup"><span data-stu-id="dafbc-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="dafbc-138">Selecione o modelo **vazio** e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafbc-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dafbc-140">Execute o comando a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="dafbc-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="dafbc-141">Um aplicativo Web ASP.NET Core vazio, direcionado ao .NET Core, será criado em um diretório *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="dafbc-142">Configurar Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="dafbc-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="dafbc-143">As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="dafbc-144">Execute o seguinte comando na raiz do projeto para criar um arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="dafbc-145">Adicione a propriedade destacada ao arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="dafbc-146">Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="dafbc-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="dafbc-147">Instalar os pacotes de npm exigidos.</span><span class="sxs-lookup"><span data-stu-id="dafbc-147">Install the required npm packages.</span></span> <span data-ttu-id="dafbc-148">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="dafbc-149">Alguns detalhes de comando a se observar:</span><span class="sxs-lookup"><span data-stu-id="dafbc-149">Some command details to note:</span></span>

    * <span data-ttu-id="dafbc-150">Um número de versão segue o sinal `@` para cada nome de pacote.</span><span class="sxs-lookup"><span data-stu-id="dafbc-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="dafbc-151">O npm instala essas versões específicas do pacote.</span><span class="sxs-lookup"><span data-stu-id="dafbc-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="dafbc-152">A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="dafbc-153">Por exemplo, `"webpack": "4.29.3"` é usado em vez de `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="dafbc-154">Essa opção evita atualizações não intencionais para versões de pacote mais recentes.</span><span class="sxs-lookup"><span data-stu-id="dafbc-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="dafbc-155">Veja os documentos oficiais de [instalação de npm](https://docs.npmjs.com/cli/install) para saber mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="dafbc-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="dafbc-156">Substitua a propriedade `scripts` do arquivo *package.json* com o seguinte snippet:</span><span class="sxs-lookup"><span data-stu-id="dafbc-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="dafbc-157">Uma explicação sobre os scripts:</span><span class="sxs-lookup"><span data-stu-id="dafbc-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="dafbc-158">`build`: agrupa os recursos do lado do cliente no modo de desenvolvimento e busca alterações no arquivo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="dafbc-159">O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado.</span><span class="sxs-lookup"><span data-stu-id="dafbc-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="dafbc-160">A opção `mode` desabilita otimizações de produção, como tree shaking e minificação.</span><span class="sxs-lookup"><span data-stu-id="dafbc-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="dafbc-161">Use somente `build` no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dafbc-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="dafbc-162">`release`: agrupa recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="dafbc-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="dafbc-163">`publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="dafbc-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="dafbc-164">Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="dafbc-165">Crie um arquivo chamado *webpack.config.js* na raiz do projeto, com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="dafbc-166">O arquivo precedente configura a compilação Webpack.</span><span class="sxs-lookup"><span data-stu-id="dafbc-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="dafbc-167">Detalhes de configuração a serem notados:</span><span class="sxs-lookup"><span data-stu-id="dafbc-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="dafbc-168">A propriedade `output` substitui o valor padrão do *dist*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="dafbc-169">Em vez disso, o lote é emitido no diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="dafbc-170">A matriz de `resolve.extensions` inclui o *. js* para importar o SignalR cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dafbc-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="dafbc-171">Crie um novo diretório *src* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="dafbc-172">O propósito é armazenar os ativos do lado do cliente do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="dafbc-173">Crie *src/index.html* com o seguinte conteúdo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="dafbc-174">A HTML precedente define a marcação clichê da página inicial.</span><span class="sxs-lookup"><span data-stu-id="dafbc-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="dafbc-175">Crie um novo diretório *src/css*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="dafbc-176">Seu propósito é armazenar os arquivos do projeto *.css*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="dafbc-177">Crie *src/css/main.css* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="dafbc-178">O arquivo precedente *main.css* define o estilo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="dafbc-179">Crie *src/tsconfig.json* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="dafbc-180">O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="dafbc-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="dafbc-181">Crie *src/index.ts* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="dafbc-182">O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="dafbc-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="dafbc-183">`keyup`: esse evento é acionado quando o usuário digita algo na caixa de texto identificada como `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="dafbc-184">A função `send` é chamada quando o usuário pressionar a tecla **Enter**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="dafbc-185">`click`: esse evento é acionado quando o usuário clica no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="dafbc-186">A função `send` é chamada.</span><span class="sxs-lookup"><span data-stu-id="dafbc-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="dafbc-187">Configurar o aplicativo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dafbc-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="dafbc-188">No método `Startup.Configure`, adicione chamadas para [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="dafbc-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="dafbc-189">O código precedente permite que o servidor localize e forneça o arquivo *index.html*, se o usuário inserir a URL completa ou a URL raiz do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="dafbc-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="dafbc-190">No final do método `Startup.Configure`, mapeie uma rota */Hub* para o Hub `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="dafbc-191">Substitua o código que exibe *Olá, mundo!*</span><span class="sxs-lookup"><span data-stu-id="dafbc-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="dafbc-192">com a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="dafbc-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="dafbc-193">No método `Startup.ConfigureServices`, chame [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="dafbc-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="dafbc-194">Ele adiciona os serviços de SignalR ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="dafbc-195">Crie um novo diretório, chamado *Hubs*, na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="dafbc-196">Sua finalidade é armazenar o Hub de SignalR, que é criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="dafbc-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="dafbc-197">Crie o hub *Hubs/ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="dafbc-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="dafbc-198">Adicione o código a seguir ao topo do arquivo *Startup.cs* para resolver a referência `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="dafbc-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="dafbc-199">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="dafbc-199">Enable client and server communication</span></span>

<span data-ttu-id="dafbc-200">Atualmente, o aplicativo exibe um formulário simples para enviar mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="dafbc-201">Nada acontece quando você tenta fazer alguma coisa.</span><span class="sxs-lookup"><span data-stu-id="dafbc-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="dafbc-202">O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.</span><span class="sxs-lookup"><span data-stu-id="dafbc-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="dafbc-203">Execute o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="dafbc-204">O comando anterior instala o [SignalR cliente TypeScript](https://www.npmjs.com/package/@microsoft/signalr), que permite ao cliente enviar mensagens para o servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="dafbc-205">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="dafbc-206">O código precedente é compatível com o recebimento de mensagens do servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="dafbc-207">A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="dafbc-208">A função `withUrl` configura a URL do hub.</span><span class="sxs-lookup"><span data-stu-id="dafbc-208">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="dafbc-209"> permite a troca de mensagens entre um cliente e um servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-209"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="dafbc-210">Cada mensagem tem um nome específico.</span><span class="sxs-lookup"><span data-stu-id="dafbc-210">Each message has a specific name.</span></span> <span data-ttu-id="dafbc-211">Por exemplo, você pode ter mensagens com o nome `messageReceived` que executam a lógica responsável por exibir a nova mensagem na zona de mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="dafbc-212">É possível escutar uma mensagem específica por meio da função `on`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="dafbc-213">Você pode escutar qualquer número de nomes de mensagem.</span><span class="sxs-lookup"><span data-stu-id="dafbc-213">You can listen to any number of message names.</span></span> <span data-ttu-id="dafbc-214">Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida.</span><span class="sxs-lookup"><span data-stu-id="dafbc-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="dafbc-215">Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="dafbc-216">Ele é adicionado ao elemento principal `div` que exibe as mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="dafbc-217">Agora que o cliente pode receber mensagens, configure-o para enviá-las.</span><span class="sxs-lookup"><span data-stu-id="dafbc-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="dafbc-218">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="dafbc-219">Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="dafbc-220">O primeiro parâmetro do método é o nome da mensagem.</span><span class="sxs-lookup"><span data-stu-id="dafbc-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="dafbc-221">Os dados da mensagem residem nos outros parâmetros.</span><span class="sxs-lookup"><span data-stu-id="dafbc-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="dafbc-222">Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="dafbc-223">A mensagem é composta do nome de usuário e da entrada em uma caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="dafbc-224">Se o envio for bem-sucedido, o valor da caixa de texto será limpo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="dafbc-225">Adicione o método em destaque à classe `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="dafbc-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="dafbc-226">O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe.</span><span class="sxs-lookup"><span data-stu-id="dafbc-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="dafbc-227">Não é necessário ter um método genérico `on` para receber todas as mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="dafbc-228">Um método nomeado com o nome da mensagem é suficiente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="dafbc-229">Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="dafbc-230">O método `NewMessage` de C# espera os dados enviados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="dafbc-231">Uma chamada é feita para o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) em [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="dafbc-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="dafbc-232">As mensagens recebidas são enviadas a todos os clientes conectados ao hub.</span><span class="sxs-lookup"><span data-stu-id="dafbc-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="dafbc-233">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dafbc-233">Test the app</span></span>

<span data-ttu-id="dafbc-234">Confirme que o aplicativo funciona com as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="dafbc-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafbc-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafbc-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dafbc-236">Execute o Webpack no modo de *versão*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="dafbc-237">Usando a janela **Console do Gerenciador de Pacotes**, execute o comando a seguir na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="dafbc-238">Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.</span><span class="sxs-lookup"><span data-stu-id="dafbc-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="dafbc-239">Selecione **Debug** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador.</span><span class="sxs-lookup"><span data-stu-id="dafbc-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="dafbc-240">O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="dafbc-241">Se você receber erros de compilação, tente fechar e reabrir a solução.</span><span class="sxs-lookup"><span data-stu-id="dafbc-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="dafbc-242">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="dafbc-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="dafbc-243">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="dafbc-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dafbc-244">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="dafbc-245">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafbc-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="dafbc-247">Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="dafbc-248">Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="dafbc-249">O servidor Web inicia o aplicativo e o disponibiliza no localhost.</span><span class="sxs-lookup"><span data-stu-id="dafbc-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="dafbc-250">Abra um navegador em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="dafbc-251">O arquivo *wwwroot/index.html* é fornecido.</span><span class="sxs-lookup"><span data-stu-id="dafbc-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="dafbc-252">Copie a URL da barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="dafbc-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="dafbc-253">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="dafbc-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="dafbc-254">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="dafbc-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dafbc-255">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="dafbc-256">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafbc-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafbc-258">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dafbc-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**</span><span class="sxs-lookup"><span data-stu-id="dafbc-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="dafbc-260">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dafbc-260">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="dafbc-261">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="dafbc-261">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafbc-262">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-262">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="dafbc-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-263">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="dafbc-264">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="dafbc-264">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="dafbc-265">C# para Visual Studio Code versão 1.17.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="dafbc-265">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="dafbc-266">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="dafbc-266">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="dafbc-267">Criar o aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dafbc-267">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafbc-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafbc-268">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dafbc-269">Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-269">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="dafbc-270">Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação.</span><span class="sxs-lookup"><span data-stu-id="dafbc-270">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="dafbc-271">Siga estas instruções no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="dafbc-271">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="dafbc-272">Navegue até **ferramentas** > **Opções** > **projetos e soluções** > **Web gerenciamento de pacotes** > **ferramentas da Web externas**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-272">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="dafbc-273">Selecione a entrada *$(PATH)* na lista.</span><span class="sxs-lookup"><span data-stu-id="dafbc-273">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="dafbc-274">Clique na seta para cima para mover a entrada para a segunda posição da lista.</span><span class="sxs-lookup"><span data-stu-id="dafbc-274">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="dafbc-276">A configuração do Visual Studio foi concluída.</span><span class="sxs-lookup"><span data-stu-id="dafbc-276">Visual Studio configuration is completed.</span></span> <span data-ttu-id="dafbc-277">É hora de criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-277">It's time to create the project.</span></span>

1. <span data-ttu-id="dafbc-278">Use a opção de menu **arquivo** > **nova** > **projeto** e escolha o modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="dafbc-278">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="dafbc-279">Nomeie o projeto *SignalRWebPack*e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-279">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="dafbc-280">Selecione *.NET Core* no menu suspenso da estrutura de destino e selecione *ASP.NET Core 2.2* no menu suspenso do seletor de estrutura.</span><span class="sxs-lookup"><span data-stu-id="dafbc-280">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="dafbc-281">Selecione o modelo **vazio** e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-281">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafbc-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dafbc-283">Execute o comando a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="dafbc-283">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="dafbc-284">Um aplicativo Web ASP.NET Core vazio, direcionado ao .NET Core, será criado em um diretório *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-284">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="dafbc-285">Configurar Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="dafbc-285">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="dafbc-286">As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-286">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="dafbc-287">Execute o seguinte comando na raiz do projeto para criar um arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-287">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="dafbc-288">Adicione a propriedade destacada ao arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-288">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="dafbc-289">Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="dafbc-289">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="dafbc-290">Instalar os pacotes de npm exigidos.</span><span class="sxs-lookup"><span data-stu-id="dafbc-290">Install the required npm packages.</span></span> <span data-ttu-id="dafbc-291">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-291">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="dafbc-292">Alguns detalhes de comando a se observar:</span><span class="sxs-lookup"><span data-stu-id="dafbc-292">Some command details to note:</span></span>

    * <span data-ttu-id="dafbc-293">Um número de versão segue o sinal `@` para cada nome de pacote.</span><span class="sxs-lookup"><span data-stu-id="dafbc-293">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="dafbc-294">O npm instala essas versões específicas do pacote.</span><span class="sxs-lookup"><span data-stu-id="dafbc-294">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="dafbc-295">A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-295">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="dafbc-296">Por exemplo, `"webpack": "4.29.3"` é usado em vez de `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-296">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="dafbc-297">Essa opção evita atualizações não intencionais para versões de pacote mais recentes.</span><span class="sxs-lookup"><span data-stu-id="dafbc-297">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="dafbc-298">Veja os documentos oficiais de [instalação de npm](https://docs.npmjs.com/cli/install) para saber mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="dafbc-298">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="dafbc-299">Substitua a propriedade `scripts` do arquivo *package.json* com o seguinte snippet:</span><span class="sxs-lookup"><span data-stu-id="dafbc-299">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="dafbc-300">Uma explicação sobre os scripts:</span><span class="sxs-lookup"><span data-stu-id="dafbc-300">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="dafbc-301">`build`: agrupa os recursos do lado do cliente no modo de desenvolvimento e busca alterações no arquivo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-301">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="dafbc-302">O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado.</span><span class="sxs-lookup"><span data-stu-id="dafbc-302">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="dafbc-303">A opção `mode` desabilita otimizações de produção, como tree shaking e minificação.</span><span class="sxs-lookup"><span data-stu-id="dafbc-303">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="dafbc-304">Use somente `build` no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="dafbc-304">Only use `build` in development.</span></span>
    * <span data-ttu-id="dafbc-305">`release`: agrupa recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="dafbc-305">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="dafbc-306">`publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="dafbc-306">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="dafbc-307">Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-307">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="dafbc-308">Crie um arquivo chamado *webpack.config.js* na raiz do projeto, com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-308">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="dafbc-309">O arquivo precedente configura a compilação Webpack.</span><span class="sxs-lookup"><span data-stu-id="dafbc-309">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="dafbc-310">Detalhes de configuração a serem notados:</span><span class="sxs-lookup"><span data-stu-id="dafbc-310">Some configuration details to note:</span></span>

    * <span data-ttu-id="dafbc-311">A propriedade `output` substitui o valor padrão do *dist*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-311">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="dafbc-312">Em vez disso, o lote é emitido no diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-312">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="dafbc-313">A matriz de `resolve.extensions` inclui o *. js* para importar o SignalR cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dafbc-313">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="dafbc-314">Crie um novo diretório *src* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-314">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="dafbc-315">O propósito é armazenar os ativos do lado do cliente do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-315">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="dafbc-316">Crie *src/index.html* com o seguinte conteúdo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-316">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="dafbc-317">A HTML precedente define a marcação clichê da página inicial.</span><span class="sxs-lookup"><span data-stu-id="dafbc-317">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="dafbc-318">Crie um novo diretório *src/css*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-318">Create a new *src/css* directory.</span></span> <span data-ttu-id="dafbc-319">Seu propósito é armazenar os arquivos do projeto *.css*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-319">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="dafbc-320">Crie *src/css/main.css* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-320">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="dafbc-321">O arquivo precedente *main.css* define o estilo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-321">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="dafbc-322">Crie *src/tsconfig.json* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-322">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="dafbc-323">O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="dafbc-323">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="dafbc-324">Crie *src/index.ts* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dafbc-324">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="dafbc-325">O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="dafbc-325">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="dafbc-326">`keyup`: esse evento é acionado quando o usuário digita algo na caixa de texto identificada como `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-326">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="dafbc-327">A função `send` é chamada quando o usuário pressionar a tecla **Enter**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-327">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="dafbc-328">`click`: esse evento é acionado quando o usuário clica no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-328">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="dafbc-329">A função `send` é chamada.</span><span class="sxs-lookup"><span data-stu-id="dafbc-329">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="dafbc-330">Configurar o aplicativo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dafbc-330">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="dafbc-331">O código fornecido no método `Startup.Configure` exibe *Olá, Mundo*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-331">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="dafbc-332">Substitua a chamada para o método `app.Run` com chamadas para [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="dafbc-332">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="dafbc-333">O código precedente permite que o servidor localize e forneça o arquivo *index.html*, se o usuário inserir a URL completa ou a URL raiz do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="dafbc-333">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="dafbc-334">Chame [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) no método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-334">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dafbc-335">Ele adiciona os serviços de SignalR ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-335">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="dafbc-336">Mapeie uma rota */hub* para o hub `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-336">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="dafbc-337">Adicione as linhas a seguir ao final do método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="dafbc-337">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="dafbc-338">Crie um novo diretório, chamado *Hubs*, na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-338">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="dafbc-339">Sua finalidade é armazenar o Hub de SignalR, que é criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="dafbc-339">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="dafbc-340">Crie o hub *Hubs/ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="dafbc-340">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="dafbc-341">Adicione o código a seguir ao topo do arquivo *Startup.cs* para resolver a referência `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="dafbc-341">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="dafbc-342">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="dafbc-342">Enable client and server communication</span></span>

<span data-ttu-id="dafbc-343">Atualmente, o aplicativo exibe um formulário simples para enviar mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-343">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="dafbc-344">Nada acontece quando você tenta fazer alguma coisa.</span><span class="sxs-lookup"><span data-stu-id="dafbc-344">Nothing happens when you try to do so.</span></span> <span data-ttu-id="dafbc-345">O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.</span><span class="sxs-lookup"><span data-stu-id="dafbc-345">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="dafbc-346">Execute o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-346">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="dafbc-347">O comando anterior instala o [SignalR cliente TypeScript](https://www.npmjs.com/package/@microsoft/signalr), que permite ao cliente enviar mensagens para o servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-347">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="dafbc-348">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-348">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="dafbc-349">O código precedente é compatível com o recebimento de mensagens do servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-349">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="dafbc-350">A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-350">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="dafbc-351">A função `withUrl` configura a URL do hub.</span><span class="sxs-lookup"><span data-stu-id="dafbc-351">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="dafbc-352"> permite a troca de mensagens entre um cliente e um servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-352"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="dafbc-353">Cada mensagem tem um nome específico.</span><span class="sxs-lookup"><span data-stu-id="dafbc-353">Each message has a specific name.</span></span> <span data-ttu-id="dafbc-354">Por exemplo, você pode ter mensagens com o nome `messageReceived` que executam a lógica responsável por exibir a nova mensagem na zona de mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-354">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="dafbc-355">É possível escutar uma mensagem específica por meio da função `on`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-355">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="dafbc-356">Você pode escutar qualquer número de nomes de mensagem.</span><span class="sxs-lookup"><span data-stu-id="dafbc-356">You can listen to any number of message names.</span></span> <span data-ttu-id="dafbc-357">Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida.</span><span class="sxs-lookup"><span data-stu-id="dafbc-357">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="dafbc-358">Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-358">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="dafbc-359">Ele é adicionado ao elemento principal `div` que exibe as mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-359">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="dafbc-360">Agora que o cliente pode receber mensagens, configure-o para enviá-las.</span><span class="sxs-lookup"><span data-stu-id="dafbc-360">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="dafbc-361">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="dafbc-361">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="dafbc-362">Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-362">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="dafbc-363">O primeiro parâmetro do método é o nome da mensagem.</span><span class="sxs-lookup"><span data-stu-id="dafbc-363">The method's first parameter is the message name.</span></span> <span data-ttu-id="dafbc-364">Os dados da mensagem residem nos outros parâmetros.</span><span class="sxs-lookup"><span data-stu-id="dafbc-364">The message data inhabits the other parameters.</span></span> <span data-ttu-id="dafbc-365">Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="dafbc-365">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="dafbc-366">A mensagem é composta do nome de usuário e da entrada em uma caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-366">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="dafbc-367">Se o envio for bem-sucedido, o valor da caixa de texto será limpo.</span><span class="sxs-lookup"><span data-stu-id="dafbc-367">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="dafbc-368">Adicione o método em destaque à classe `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="dafbc-368">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="dafbc-369">O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe.</span><span class="sxs-lookup"><span data-stu-id="dafbc-369">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="dafbc-370">Não é necessário ter um método genérico `on` para receber todas as mensagens.</span><span class="sxs-lookup"><span data-stu-id="dafbc-370">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="dafbc-371">Um método nomeado com o nome da mensagem é suficiente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-371">A method named after the message name suffices.</span></span>

    <span data-ttu-id="dafbc-372">Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-372">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="dafbc-373">O método `NewMessage` de C# espera os dados enviados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-373">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="dafbc-374">Uma chamada é feita para o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) em [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="dafbc-374">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="dafbc-375">As mensagens recebidas são enviadas a todos os clientes conectados ao hub.</span><span class="sxs-lookup"><span data-stu-id="dafbc-375">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="dafbc-376">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="dafbc-376">Test the app</span></span>

<span data-ttu-id="dafbc-377">Confirme que o aplicativo funciona com as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="dafbc-377">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dafbc-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dafbc-378">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="dafbc-379">Execute o Webpack no modo de *versão*.</span><span class="sxs-lookup"><span data-stu-id="dafbc-379">Run Webpack in *release* mode.</span></span> <span data-ttu-id="dafbc-380">Usando a janela **Console do Gerenciador de Pacotes**, execute o comando a seguir na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="dafbc-380">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="dafbc-381">Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.</span><span class="sxs-lookup"><span data-stu-id="dafbc-381">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="dafbc-382">Selecione **Debug** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador.</span><span class="sxs-lookup"><span data-stu-id="dafbc-382">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="dafbc-383">O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-383">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="dafbc-384">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="dafbc-384">Open another browser instance (any browser).</span></span> <span data-ttu-id="dafbc-385">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="dafbc-385">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dafbc-386">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-386">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="dafbc-387">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-387">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dafbc-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dafbc-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="dafbc-389">Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-389">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="dafbc-390">Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="dafbc-390">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="dafbc-391">O servidor Web inicia o aplicativo e o disponibiliza no localhost.</span><span class="sxs-lookup"><span data-stu-id="dafbc-391">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="dafbc-392">Abra um navegador em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="dafbc-392">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="dafbc-393">O arquivo *wwwroot/index.html* é fornecido.</span><span class="sxs-lookup"><span data-stu-id="dafbc-393">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="dafbc-394">Copie a URL da barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="dafbc-394">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="dafbc-395">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="dafbc-395">Open another browser instance (any browser).</span></span> <span data-ttu-id="dafbc-396">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="dafbc-396">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="dafbc-397">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="dafbc-397">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="dafbc-398">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="dafbc-398">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="dafbc-400">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dafbc-400">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
