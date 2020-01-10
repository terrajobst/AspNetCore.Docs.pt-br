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
ms.openlocfilehash: 9094a1d391c087a6f58aa9dd66e3697a79f4af86
ms.sourcegitcommit: ef1720cb733908f36a54825d84c3461c5280bdbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737510"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="5cfd8-103">Usar ASP.NET Core SignalR com TypeScript e webpack</span><span class="sxs-lookup"><span data-stu-id="5cfd8-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="5cfd8-104">Por [Sébastien Sougnez](https://twitter.com/ssougnez) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="5cfd8-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="5cfd8-105">O [Webpack](https://webpack.js.org/) habilita os desenvolvedores a agrupar e criar recursos de um aplicativo Web do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="5cfd8-106">Este tutorial demonstra como usar o webpack em um ASP.NET Core SignalR aplicativo Web cujo cliente é escrito em [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="5cfd8-107">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5cfd8-108">Scaffold um aplicativo de SignalR inicial ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5cfd8-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="5cfd8-109">Configurar o cliente TypeScript SignalR</span><span class="sxs-lookup"><span data-stu-id="5cfd8-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="5cfd8-110">Configurar um pipeline de build usando o Webpack</span><span class="sxs-lookup"><span data-stu-id="5cfd8-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="5cfd8-111">Configurar o servidor de SignalR</span><span class="sxs-lookup"><span data-stu-id="5cfd8-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="5cfd8-112">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="5cfd8-112">Enable communication between client and server</span></span>

<span data-ttu-id="5cfd8-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5cfd8-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="5cfd8-114">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="5cfd8-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cfd8-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cfd8-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5cfd8-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**</span><span class="sxs-lookup"><span data-stu-id="5cfd8-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="5cfd8-117">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="5cfd8-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="5cfd8-118">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="5cfd8-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cfd8-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="5cfd8-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="5cfd8-121">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="5cfd8-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="5cfd8-122">C# para Visual Studio Code versão 1.17.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="5cfd8-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="5cfd8-123">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="5cfd8-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="5cfd8-124">Criar o aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5cfd8-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cfd8-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cfd8-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5cfd8-126">Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="5cfd8-127">Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="5cfd8-128">Siga estas instruções no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="5cfd8-129">Navegue até **ferramentas** > **Opções** > **projetos e soluções** > **Web gerenciamento de pacotes** > **ferramentas da Web externas**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="5cfd8-130">Selecione a entrada *$(PATH)* na lista.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="5cfd8-131">Clique na seta para cima para mover a entrada para a segunda posição da lista.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="5cfd8-133">A configuração do Visual Studio foi concluída.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="5cfd8-134">É hora de criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-134">It's time to create the project.</span></span>

1. <span data-ttu-id="5cfd8-135">Use a opção de menu **arquivo** > **nova** > **projeto** e escolha o modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="5cfd8-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="5cfd8-136">Nomeie o projeto *SignalRWebPack*e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="5cfd8-137">Selecione *.NET Core* na lista suspensa estrutura de destino e selecione *ASP.NET Core 3,0* na lista suspensa seletor de estrutura.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="5cfd8-138">Selecione o modelo **vazio** e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cfd8-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5cfd8-140">Execute o comando a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="5cfd8-141">Um aplicativo Web ASP.NET Core vazio, direcionado ao .NET Core, será criado em um diretório *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="5cfd8-142">Configurar Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="5cfd8-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="5cfd8-143">As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="5cfd8-144">Execute o seguinte comando na raiz do projeto para criar um arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="5cfd8-145">Adicione a propriedade destacada ao arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="5cfd8-146">Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="5cfd8-147">Instalar os pacotes de npm exigidos.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-147">Install the required npm packages.</span></span> <span data-ttu-id="5cfd8-148">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="5cfd8-149">Alguns detalhes de comando a se observar:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-149">Some command details to note:</span></span>

    * <span data-ttu-id="5cfd8-150">Um número de versão segue o sinal `@` para cada nome de pacote.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="5cfd8-151">O npm instala essas versões específicas do pacote.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="5cfd8-152">A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="5cfd8-153">Por exemplo, `"webpack": "4.29.3"` é usado em vez de `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="5cfd8-154">Essa opção evita atualizações não intencionais para versões de pacote mais recentes.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="5cfd8-155">Veja os documentos oficiais de [instalação de npm](https://docs.npmjs.com/cli/install) para saber mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="5cfd8-156">Substitua a propriedade `scripts` do arquivo *package.json* com o seguinte snippet:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="5cfd8-157">Uma explicação sobre os scripts:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="5cfd8-158">`build`: agrupa os recursos do lado do cliente no modo de desenvolvimento e busca alterações no arquivo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="5cfd8-159">O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="5cfd8-160">A opção `mode` desabilita otimizações de produção, como tree shaking e minificação.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="5cfd8-161">Use somente `build` no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="5cfd8-162">`release`: agrupa recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="5cfd8-163">`publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="5cfd8-164">Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="5cfd8-165">Crie um arquivo chamado *webpack.config.js* na raiz do projeto, com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="5cfd8-166">O arquivo precedente configura a compilação Webpack.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="5cfd8-167">Detalhes de configuração a serem notados:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="5cfd8-168">A propriedade `output` substitui o valor padrão do *dist*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="5cfd8-169">Em vez disso, o lote é emitido no diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="5cfd8-170">A matriz de `resolve.extensions` inclui o *. js* para importar o SignalR cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="5cfd8-171">Crie um novo diretório *src* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="5cfd8-172">O propósito é armazenar os ativos do lado do cliente do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="5cfd8-173">Crie *src/index.html* com o seguinte conteúdo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="5cfd8-174">A HTML precedente define a marcação clichê da página inicial.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="5cfd8-175">Crie um novo diretório *src/css*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="5cfd8-176">Seu propósito é armazenar os arquivos do projeto *.css*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="5cfd8-177">Crie *src/css/main.css* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="5cfd8-178">O arquivo precedente *main.css* define o estilo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="5cfd8-179">Crie *src/tsconfig.json* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="5cfd8-180">O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="5cfd8-181">Crie *src/index.ts* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="5cfd8-182">O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="5cfd8-183">`keyup`: esse evento é acionado quando o usuário digita algo na caixa de texto identificada como `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="5cfd8-184">A função `send` é chamada quando o usuário pressionar a tecla **Enter**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="5cfd8-185">`click`: esse evento é acionado quando o usuário clica no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="5cfd8-186">A função `send` é chamada.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="5cfd8-187">Configurar o aplicativo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5cfd8-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="5cfd8-188">No método `Startup.Configure`, adicione chamadas para [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="5cfd8-189">O código precedente permite que o servidor localize e forneça o arquivo *index.html*, se o usuário inserir a URL completa ou a URL raiz do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="5cfd8-190">No final do método `Startup.Configure`, mapeie uma rota */Hub* para o Hub `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="5cfd8-191">Substitua o código que exibe *Olá, mundo!*</span><span class="sxs-lookup"><span data-stu-id="5cfd8-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="5cfd8-192">pela seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="5cfd8-193">No método `Startup.ConfigureServices`, chame [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="5cfd8-194">Ele adiciona os serviços de SignalR ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="5cfd8-195">Crie um novo diretório, chamado *Hubs*, na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="5cfd8-196">Sua finalidade é armazenar o Hub de SignalR, que é criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="5cfd8-197">Crie o hub *Hubs/ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="5cfd8-198">Adicione o código a seguir ao topo do arquivo *Startup.cs* para resolver a referência `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="5cfd8-199">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="5cfd8-199">Enable client and server communication</span></span>

<span data-ttu-id="5cfd8-200">Atualmente, o aplicativo exibe um formulário simples para enviar mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="5cfd8-201">Nada acontece quando você tenta fazer alguma coisa.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="5cfd8-202">O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="5cfd8-203">Execute o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="5cfd8-204">O comando anterior instala o [SignalR cliente TypeScript](https://www.npmjs.com/package/@microsoft/signalr), que permite ao cliente enviar mensagens para o servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="5cfd8-205">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="5cfd8-206">O código precedente é compatível com o recebimento de mensagens do servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="5cfd8-207">A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="5cfd8-208">A função `withUrl` configura a URL do hub.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-208">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="5cfd8-209"> permite a troca de mensagens entre um cliente e um servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-209"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="5cfd8-210">Cada mensagem tem um nome específico.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-210">Each message has a specific name.</span></span> <span data-ttu-id="5cfd8-211">Por exemplo, você pode ter mensagens com o nome `messageReceived` que executam a lógica responsável por exibir a nova mensagem na zona de mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="5cfd8-212">É possível escutar uma mensagem específica por meio da função `on`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="5cfd8-213">Você pode escutar qualquer número de nomes de mensagem.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-213">You can listen to any number of message names.</span></span> <span data-ttu-id="5cfd8-214">Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="5cfd8-215">Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="5cfd8-216">Ele é adicionado ao elemento principal `div` que exibe as mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="5cfd8-217">Agora que o cliente pode receber mensagens, configure-o para enviá-las.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="5cfd8-218">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="5cfd8-219">Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="5cfd8-220">O primeiro parâmetro do método é o nome da mensagem.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="5cfd8-221">Os dados da mensagem residem nos outros parâmetros.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="5cfd8-222">Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="5cfd8-223">A mensagem é composta do nome de usuário e da entrada em uma caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="5cfd8-224">Se o envio for bem-sucedido, o valor da caixa de texto será limpo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="5cfd8-225">Adicione o método em destaque à classe `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="5cfd8-226">O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="5cfd8-227">Não é necessário ter um método genérico `on` para receber todas as mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="5cfd8-228">Um método nomeado com o nome da mensagem é suficiente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="5cfd8-229">Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="5cfd8-230">O método `NewMessage` de C# espera os dados enviados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="5cfd8-231">Uma chamada é feita para o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) em [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="5cfd8-232">As mensagens recebidas são enviadas a todos os clientes conectados ao hub.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="5cfd8-233">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="5cfd8-233">Test the app</span></span>

<span data-ttu-id="5cfd8-234">Confirme que o aplicativo funciona com as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cfd8-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cfd8-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5cfd8-236">Execute o Webpack no modo de *versão*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="5cfd8-237">Usando a janela **Console do Gerenciador de Pacotes**, execute o comando a seguir na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="5cfd8-238">Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="5cfd8-239">Selecione **Debug** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="5cfd8-240">O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="5cfd8-241">Se você receber erros de compilação, tente fechar e reabrir a solução.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="5cfd8-242">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="5cfd8-243">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5cfd8-244">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="5cfd8-245">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cfd8-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5cfd8-247">Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="5cfd8-248">Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="5cfd8-249">O servidor Web inicia o aplicativo e o disponibiliza no localhost.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="5cfd8-250">Abra um navegador em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="5cfd8-251">O arquivo *wwwroot/index.html* é fornecido.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="5cfd8-252">Copie a URL da barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="5cfd8-253">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="5cfd8-254">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5cfd8-255">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="5cfd8-256">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="5cfd8-258">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="5cfd8-258">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cfd8-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cfd8-259">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5cfd8-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**</span><span class="sxs-lookup"><span data-stu-id="5cfd8-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="5cfd8-261">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="5cfd8-261">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="5cfd8-262">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="5cfd8-262">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cfd8-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-263">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="5cfd8-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-264">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="5cfd8-265">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="5cfd8-265">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="5cfd8-266">C# para Visual Studio Code versão 1.17.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="5cfd8-266">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="5cfd8-267">[Node.js](https://nodejs.org/) com [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="5cfd8-267">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="5cfd8-268">Criar o aplicativo Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5cfd8-268">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cfd8-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cfd8-269">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5cfd8-270">Configure o Visual Studio para pesquisar o npm na variável de ambiente *PATH*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-270">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="5cfd8-271">Por padrão, o Visual Studio usa a versão do npm encontrada no diretório de instalação.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-271">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="5cfd8-272">Siga estas instruções no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-272">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="5cfd8-273">Navegue até **ferramentas** > **Opções** > **projetos e soluções** > **Web gerenciamento de pacotes** > **ferramentas da Web externas**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-273">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="5cfd8-274">Selecione a entrada *$(PATH)* na lista.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-274">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="5cfd8-275">Clique na seta para cima para mover a entrada para a segunda posição da lista.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-275">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Configuração do Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="5cfd8-277">A configuração do Visual Studio foi concluída.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-277">Visual Studio configuration is completed.</span></span> <span data-ttu-id="5cfd8-278">É hora de criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-278">It's time to create the project.</span></span>

1. <span data-ttu-id="5cfd8-279">Use a opção de menu **arquivo** > **nova** > **projeto** e escolha o modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="5cfd8-279">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="5cfd8-280">Nomeie o projeto *SignalRWebPack*e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-280">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="5cfd8-281">Selecione *.NET Core* no menu suspenso da estrutura de destino e selecione *ASP.NET Core 2.2* no menu suspenso do seletor de estrutura.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-281">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="5cfd8-282">Selecione o modelo **vazio** e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-282">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cfd8-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5cfd8-284">Execute o comando a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-284">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="5cfd8-285">Um aplicativo Web ASP.NET Core vazio, direcionado ao .NET Core, será criado em um diretório *SignalRWebPack*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-285">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="5cfd8-286">Configurar Webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="5cfd8-286">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="5cfd8-287">As etapas a seguir configuram a conversão do TypeScript para JavaScript e o agrupamento de recursos no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-287">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="5cfd8-288">Execute o seguinte comando na raiz do projeto para criar um arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-288">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="5cfd8-289">Adicione a propriedade destacada ao arquivo *package.json*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-289">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="5cfd8-290">Definir a propriedade `private` como `true` evita avisos de instalação de pacote na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-290">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="5cfd8-291">Instalar os pacotes de npm exigidos.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-291">Install the required npm packages.</span></span> <span data-ttu-id="5cfd8-292">Execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-292">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="5cfd8-293">Alguns detalhes de comando a se observar:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-293">Some command details to note:</span></span>

    * <span data-ttu-id="5cfd8-294">Um número de versão segue o sinal `@` para cada nome de pacote.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-294">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="5cfd8-295">O npm instala essas versões específicas do pacote.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-295">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="5cfd8-296">A opção `-E` desabilita o comportamento padrão do npm de escrever os operadores de intervalo de [versão semântica](https://semver.org/) para *package.json*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-296">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="5cfd8-297">Por exemplo, `"webpack": "4.29.3"` é usado em vez de `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-297">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="5cfd8-298">Essa opção evita atualizações não intencionais para versões de pacote mais recentes.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-298">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="5cfd8-299">Veja os documentos oficiais de [instalação de npm](https://docs.npmjs.com/cli/install) para saber mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-299">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="5cfd8-300">Substitua a propriedade `scripts` do arquivo *package.json* com o seguinte snippet:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-300">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="5cfd8-301">Uma explicação sobre os scripts:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-301">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="5cfd8-302">`build`: agrupa os recursos do lado do cliente no modo de desenvolvimento e busca alterações no arquivo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-302">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="5cfd8-303">O observador de arquivos faz com que o lote se regenere toda vez que um arquivo de projeto é alterado.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-303">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="5cfd8-304">A opção `mode` desabilita otimizações de produção, como tree shaking e minificação.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-304">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="5cfd8-305">Use somente `build` no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-305">Only use `build` in development.</span></span>
    * <span data-ttu-id="5cfd8-306">`release`: agrupa recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-306">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="5cfd8-307">`publish`: executa o script `release` para agrupar recursos do lado do cliente no modo de produção.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-307">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="5cfd8-308">Chama o comando [publicar](/dotnet/core/tools/dotnet-publish) da CLI do .NET Core para publicar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-308">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="5cfd8-309">Crie um arquivo chamado *webpack.config.js* na raiz do projeto, com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-309">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="5cfd8-310">O arquivo precedente configura a compilação Webpack.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-310">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="5cfd8-311">Detalhes de configuração a serem notados:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-311">Some configuration details to note:</span></span>

    * <span data-ttu-id="5cfd8-312">A propriedade `output` substitui o valor padrão do *dist*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-312">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="5cfd8-313">Em vez disso, o lote é emitido no diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-313">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="5cfd8-314">A matriz de `resolve.extensions` inclui o *. js* para importar o SignalR cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-314">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="5cfd8-315">Crie um novo diretório *src* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-315">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="5cfd8-316">O propósito é armazenar os ativos do lado do cliente do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-316">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="5cfd8-317">Crie *src/index.html* com o seguinte conteúdo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-317">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="5cfd8-318">A HTML precedente define a marcação clichê da página inicial.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-318">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="5cfd8-319">Crie um novo diretório *src/css*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-319">Create a new *src/css* directory.</span></span> <span data-ttu-id="5cfd8-320">Seu propósito é armazenar os arquivos do projeto *.css*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-320">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="5cfd8-321">Crie *src/css/main.css* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-321">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="5cfd8-322">O arquivo precedente *main.css* define o estilo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-322">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="5cfd8-323">Crie *src/tsconfig.json* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-323">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="5cfd8-324">O código precedente configura o compilador TypeScript para produzir um JavaScript compatível com [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-324">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="5cfd8-325">Crie *src/index.ts* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-325">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="5cfd8-326">O TypeScript precedente recupera referências a elementos DOM e anexa dois manipuladores de eventos:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-326">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="5cfd8-327">`keyup`: esse evento é acionado quando o usuário digita algo na caixa de texto identificada como `tbMessage`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-327">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="5cfd8-328">A função `send` é chamada quando o usuário pressionar a tecla **Enter**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-328">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="5cfd8-329">`click`: esse evento é acionado quando o usuário clica no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-329">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="5cfd8-330">A função `send` é chamada.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-330">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="5cfd8-331">Configurar o aplicativo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5cfd8-331">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="5cfd8-332">O código fornecido no método `Startup.Configure` exibe *Olá, Mundo*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-332">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="5cfd8-333">Substitua a chamada para o método `app.Run` com chamadas para [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-333">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="5cfd8-334">O código precedente permite que o servidor localize e forneça o arquivo *index.html*, se o usuário inserir a URL completa ou a URL raiz do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-334">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="5cfd8-335">Chame [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) no método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-335">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5cfd8-336">Ele adiciona os serviços de SignalR ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-336">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="5cfd8-337">Mapeie uma rota */hub* para o hub `ChatHub`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-337">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="5cfd8-338">Adicione as linhas a seguir ao final do método `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-338">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="5cfd8-339">Crie um novo diretório, chamado *Hubs*, na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-339">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="5cfd8-340">Sua finalidade é armazenar o Hub de SignalR, que é criado na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-340">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="5cfd8-341">Crie o hub *Hubs/ChatHub.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-341">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="5cfd8-342">Adicione o código a seguir ao topo do arquivo *Startup.cs* para resolver a referência `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-342">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="5cfd8-343">Habilitar a comunicação entre o cliente e o servidor</span><span class="sxs-lookup"><span data-stu-id="5cfd8-343">Enable client and server communication</span></span>

<span data-ttu-id="5cfd8-344">Atualmente, o aplicativo exibe um formulário simples para enviar mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-344">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="5cfd8-345">Nada acontece quando você tenta fazer alguma coisa.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-345">Nothing happens when you try to do so.</span></span> <span data-ttu-id="5cfd8-346">O servidor está escutando uma rota específica, mas não faz nada com as mensagens enviadas.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-346">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="5cfd8-347">Execute o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-347">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="5cfd8-348">O comando anterior instala o [SignalR cliente TypeScript](https://www.npmjs.com/package/@microsoft/signalr), que permite ao cliente enviar mensagens para o servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-348">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="5cfd8-349">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-349">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="5cfd8-350">O código precedente é compatível com o recebimento de mensagens do servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-350">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="5cfd8-351">A classe `HubConnectionBuilder` cria um novo construtor para configurar a conexão do servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-351">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="5cfd8-352">A função `withUrl` configura a URL do hub.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-352">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="5cfd8-353"> permite a troca de mensagens entre um cliente e um servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-353"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="5cfd8-354">Cada mensagem tem um nome específico.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-354">Each message has a specific name.</span></span> <span data-ttu-id="5cfd8-355">Por exemplo, você pode ter mensagens com o nome `messageReceived` que executam a lógica responsável por exibir a nova mensagem na zona de mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-355">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="5cfd8-356">É possível escutar uma mensagem específica por meio da função `on`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-356">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="5cfd8-357">Você pode escutar qualquer número de nomes de mensagem.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-357">You can listen to any number of message names.</span></span> <span data-ttu-id="5cfd8-358">Também é possível passar parâmetros para a mensagem, como o nome do autor e o conteúdo da mensagem recebida.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-358">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="5cfd8-359">Quando o cliente recebe a mensagem, um novo elemento `div` é criado com o nome do autor e o conteúdo da mensagem em seu atributo `innerHTML`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-359">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="5cfd8-360">Ele é adicionado ao elemento principal `div` que exibe as mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-360">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="5cfd8-361">Agora que o cliente pode receber mensagens, configure-o para enviá-las.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-361">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="5cfd8-362">Adicione o código destacado ao arquivo *src/index.ts*:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-362">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="5cfd8-363">Enviar uma mensagem por meio da conexão WebSockets exige uma chamada para o método `send`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-363">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="5cfd8-364">O primeiro parâmetro do método é o nome da mensagem.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-364">The method's first parameter is the message name.</span></span> <span data-ttu-id="5cfd8-365">Os dados da mensagem residem nos outros parâmetros.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-365">The message data inhabits the other parameters.</span></span> <span data-ttu-id="5cfd8-366">Neste exemplo, uma mensagem identificada como `newMessage` é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-366">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="5cfd8-367">A mensagem é composta do nome de usuário e da entrada em uma caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-367">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="5cfd8-368">Se o envio for bem-sucedido, o valor da caixa de texto será limpo.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-368">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="5cfd8-369">Adicione o método em destaque à classe `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-369">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="5cfd8-370">O código precedente transmite as mensagens recebidas para todos os usuários conectados quando o servidor as recebe.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-370">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="5cfd8-371">Não é necessário ter um método genérico `on` para receber todas as mensagens.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-371">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="5cfd8-372">Um método nomeado com o nome da mensagem é suficiente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-372">A method named after the message name suffices.</span></span>

    <span data-ttu-id="5cfd8-373">Neste exemplo, o cliente TypeScript envia uma mensagem identificada como `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-373">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="5cfd8-374">O método `NewMessage` de C# espera os dados enviados pelo cliente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-374">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="5cfd8-375">Uma chamada é feita para o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) em [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-375">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="5cfd8-376">As mensagens recebidas são enviadas a todos os clientes conectados ao hub.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-376">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="5cfd8-377">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="5cfd8-377">Test the app</span></span>

<span data-ttu-id="5cfd8-378">Confirme que o aplicativo funciona com as seguintes etapas.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-378">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5cfd8-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5cfd8-379">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5cfd8-380">Execute o Webpack no modo de *versão*.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-380">Run Webpack in *release* mode.</span></span> <span data-ttu-id="5cfd8-381">Usando a janela **Console do Gerenciador de Pacotes**, execute o comando a seguir na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-381">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="5cfd8-382">Se você não estiver na raiz do projeto, insira `cd SignalRWebPack` antes de inserir o comando.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-382">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="5cfd8-383">Selecione **Debug** > **Iniciar sem depuração** para iniciar o aplicativo em um navegador sem anexar o depurador.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-383">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="5cfd8-384">O arquivo *wwwroot/index.html* é fornecido em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-384">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="5cfd8-385">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-385">Open another browser instance (any browser).</span></span> <span data-ttu-id="5cfd8-386">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-386">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5cfd8-387">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-387">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="5cfd8-388">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-388">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5cfd8-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5cfd8-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5cfd8-390">Execute o Webpack no modo de *versão* executando o comando a seguir na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-390">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="5cfd8-391">Crie e execute o aplicativo executando o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="5cfd8-391">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="5cfd8-392">O servidor Web inicia o aplicativo e o disponibiliza no localhost.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-392">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="5cfd8-393">Abra um navegador em `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-393">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="5cfd8-394">O arquivo *wwwroot/index.html* é fornecido.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-394">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="5cfd8-395">Copie a URL da barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-395">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="5cfd8-396">Abra outra instância do navegador (qualquer navegador).</span><span class="sxs-lookup"><span data-stu-id="5cfd8-396">Open another browser instance (any browser).</span></span> <span data-ttu-id="5cfd8-397">Cole a URL na barra de endereços.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-397">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5cfd8-398">Escolha qualquer navegador, digite algo na caixa de texto **Mensagem** e clique no botão **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-398">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="5cfd8-399">O nome de usuário exclusivo e a mensagem são exibidas em ambas as páginas instantaneamente.</span><span class="sxs-lookup"><span data-stu-id="5cfd8-399">The unique user name and message are displayed on both pages instantly.</span></span>

---

![mensagem exibida em ambas as janelas do navegador](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5cfd8-401">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5cfd8-401">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
