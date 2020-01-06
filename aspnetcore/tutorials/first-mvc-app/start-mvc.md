---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: e70384a6f20f3ef06059ed6b51c76e923187c317
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354933"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="591f5-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="591f5-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="591f5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="591f5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="591f5-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="591f5-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="591f5-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="591f5-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="591f5-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="591f5-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="591f5-108">Criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="591f5-108">Create a web app.</span></span>
> * <span data-ttu-id="591f5-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="591f5-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="591f5-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="591f5-110">Work with a database.</span></span>
> * <span data-ttu-id="591f5-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="591f5-111">Add search and validation.</span></span>

<span data-ttu-id="591f5-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="591f5-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="591f5-113">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="591f5-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="591f5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="591f5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="591f5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="591f5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="591f5-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="591f5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="591f5-117">Como criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="591f5-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="591f5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="591f5-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="591f5-119">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="591f5-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="591f5-120">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="591f5-122">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="591f5-123">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="591f5-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="591f5-125">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="591f5-126">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="591f5-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="591f5-127">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="591f5-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="591f5-128">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="591f5-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="591f5-129">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="591f5-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="591f5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="591f5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="591f5-131">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="591f5-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="591f5-132">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="591f5-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="591f5-133">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="591f5-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="591f5-134">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="591f5-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="591f5-135">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="591f5-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="591f5-136">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="591f5-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="591f5-137">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="591f5-137">Select **Yes**</span></span>

  * <span data-ttu-id="591f5-138">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="591f5-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="591f5-139">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="591f5-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="591f5-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="591f5-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="591f5-141">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="591f5-141">Select **File** > **New Solution**.</span></span>

  ![Nova Solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="591f5-143">Selecione **.NET Core** > **aplicativo** > **aplicativo Web (Model-View-Controller)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="591f5-145">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** do **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="591f5-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="591f5-146">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="591f5-147">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="591f5-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="591f5-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="591f5-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="591f5-149">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="591f5-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="591f5-150">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="591f5-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="591f5-151">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="591f5-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="591f5-152">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="591f5-153">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="591f5-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="591f5-154">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="591f5-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="591f5-155">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="591f5-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="591f5-156">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="591f5-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="591f5-158">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="591f5-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="591f5-160">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="591f5-160">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="591f5-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="591f5-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="591f5-163">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="591f5-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="591f5-164">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="591f5-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="591f5-165">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="591f5-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="591f5-166">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="591f5-167">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="591f5-168">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="591f5-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="591f5-169">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="591f5-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="591f5-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="591f5-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="591f5-172">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="591f5-172">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="591f5-173">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="591f5-173">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="591f5-174">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="591f5-174">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="591f5-175">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-175">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="591f5-176">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="591f5-176">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="591f5-177">Quando você executar o aplicativo, verá um número de porta diferente.</span><span class="sxs-lookup"><span data-stu-id="591f5-177">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="591f5-178">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-178">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="591f5-179">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="591f5-179">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="591f5-181">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="591f5-181">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="591f5-182">Avançar</span><span class="sxs-lookup"><span data-stu-id="591f5-182">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="591f5-183">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="591f5-183">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="591f5-184">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="591f5-184">The app manages a database of movie titles.</span></span> <span data-ttu-id="591f5-185">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="591f5-185">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="591f5-186">Criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="591f5-186">Create a web app.</span></span>
> * <span data-ttu-id="591f5-187">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="591f5-187">Add and scaffold a model.</span></span>
> * <span data-ttu-id="591f5-188">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="591f5-188">Work with a database.</span></span>
> * <span data-ttu-id="591f5-189">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="591f5-189">Add search and validation.</span></span>

<span data-ttu-id="591f5-190">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="591f5-190">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="591f5-191">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="591f5-191">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="591f5-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="591f5-192">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="591f5-193">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="591f5-193">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="591f5-194">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="591f5-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="591f5-195">Como criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="591f5-195">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="591f5-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="591f5-196">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="591f5-197">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="591f5-197">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="591f5-198">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-198">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="591f5-200">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-200">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="591f5-201">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="591f5-201">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="591f5-203">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-203">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="591f5-204">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="591f5-204">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="591f5-205">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="591f5-205">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="591f5-206">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="591f5-206">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="591f5-207">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="591f5-207">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="591f5-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="591f5-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="591f5-209">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="591f5-209">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="591f5-210">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="591f5-210">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="591f5-211">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="591f5-211">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="591f5-212">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="591f5-212">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="591f5-213">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="591f5-213">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="591f5-214">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="591f5-214">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="591f5-215">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="591f5-215">Select **Yes**</span></span>

  * <span data-ttu-id="591f5-216">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="591f5-216">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="591f5-217">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="591f5-217">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="591f5-218">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="591f5-218">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="591f5-219">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="591f5-219">Select **File** > **New Solution**.</span></span>

  ![Nova Solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="591f5-221">Selecione **.NET Core** > **aplicativo** > **aplicativo Web (Model-View-Controller)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-221">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="591f5-223">Na caixa de diálogo **Configurar a nova API Web com o ASP.NET Core**, aceite a **Estrutura de Destino** padrão, que é o **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="591f5-223">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Seleção do .NET Core 2.2 do macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="591f5-225">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-225">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="591f5-226">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="591f5-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="591f5-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="591f5-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="591f5-228">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="591f5-228">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="591f5-229">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="591f5-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="591f5-230">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="591f5-230">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="591f5-231">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-231">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="591f5-232">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="591f5-232">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="591f5-233">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="591f5-233">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="591f5-234">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="591f5-234">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="591f5-235">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="591f5-235">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="591f5-237">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="591f5-237">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="591f5-239">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="591f5-239">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="591f5-240">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="591f5-240">This app doesn't track personal information.</span></span> <span data-ttu-id="591f5-241">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="591f5-241">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="591f5-243">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="591f5-243">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="591f5-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="591f5-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="591f5-246">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="591f5-246">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="591f5-247">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="591f5-247">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="591f5-248">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="591f5-248">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="591f5-249">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-249">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="591f5-250">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-250">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="591f5-251">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="591f5-251">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="591f5-252">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="591f5-252">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="591f5-253">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="591f5-253">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="591f5-254">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="591f5-254">This app doesn't track personal information.</span></span> <span data-ttu-id="591f5-255">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="591f5-255">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="591f5-257">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="591f5-257">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="591f5-259">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="591f5-259">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="591f5-260">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="591f5-260">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="591f5-261">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="591f5-261">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="591f5-262">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="591f5-262">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="591f5-263">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="591f5-263">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="591f5-264">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="591f5-264">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="591f5-265">Quando você executar o aplicativo, verá um número de porta diferente.</span><span class="sxs-lookup"><span data-stu-id="591f5-265">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="591f5-266">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="591f5-266">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="591f5-267">Selecione **Aceitar** para dar consentimento ao acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="591f5-267">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="591f5-268">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="591f5-268">This app doesn't track personal information.</span></span> <span data-ttu-id="591f5-269">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="591f5-269">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="591f5-271">A imagem a seguir mostra o aplicativo depois de aceitar o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="591f5-271">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="591f5-273">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="591f5-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="591f5-274">Avançar</span><span class="sxs-lookup"><span data-stu-id="591f5-274">Next</span></span>](adding-controller.md)

::: moniker-end
