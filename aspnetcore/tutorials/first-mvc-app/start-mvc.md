---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662473"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="254a1-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="254a1-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="254a1-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="254a1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="254a1-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="254a1-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="254a1-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="254a1-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="254a1-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="254a1-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="254a1-108">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="254a1-108">Create a web app.</span></span>
> * <span data-ttu-id="254a1-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="254a1-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="254a1-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="254a1-110">Work with a database.</span></span>
> * <span data-ttu-id="254a1-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="254a1-111">Add search and validation.</span></span>

<span data-ttu-id="254a1-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="254a1-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="254a1-113">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="254a1-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254a1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254a1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="254a1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254a1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254a1-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="254a1-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="254a1-117">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="254a1-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254a1-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254a1-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="254a1-119">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="254a1-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="254a1-120">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="254a1-122">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="254a1-123">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="254a1-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="254a1-125">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="254a1-126">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="254a1-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="254a1-127">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="254a1-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="254a1-128">Você pode ter um aplicativo funcionando agora mesmo digitando um nome de projeto e selecionando algumas opções.</span><span class="sxs-lookup"><span data-stu-id="254a1-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="254a1-129">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="254a1-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="254a1-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254a1-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="254a1-131">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="254a1-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="254a1-132">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="254a1-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="254a1-133">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="254a1-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="254a1-134">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="254a1-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="254a1-135">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="254a1-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="254a1-136">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="254a1-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="254a1-137">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="254a1-137">Select **Yes**</span></span>

  * <span data-ttu-id="254a1-138">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="254a1-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="254a1-139">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="254a1-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254a1-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="254a1-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="254a1-141">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="254a1-141">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="254a1-143">Selecione **.NET Core** > **aplicativo** > **aplicativo Web (Model-View-Controller)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="254a1-145">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** do **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="254a1-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![seleção do macOS .NET Core 3,1](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="254a1-147">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="254a1-148">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="254a1-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254a1-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254a1-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="254a1-150">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="254a1-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="254a1-151">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="254a1-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="254a1-152">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="254a1-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="254a1-153">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="254a1-154">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="254a1-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="254a1-155">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="254a1-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="254a1-156">Muitos desenvolvedores preferem usar o modo de não depuração para iniciar o aplicativo rapidamente e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="254a1-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="254a1-157">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="254a1-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="254a1-159">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="254a1-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="254a1-161">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="254a1-161">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="254a1-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254a1-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="254a1-164">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="254a1-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="254a1-165">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="254a1-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="254a1-166">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="254a1-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="254a1-167">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="254a1-168">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="254a1-169">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="254a1-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="254a1-170">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="254a1-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254a1-172">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="254a1-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="254a1-173">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="254a1-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="254a1-174">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="254a1-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="254a1-175">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="254a1-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="254a1-176">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="254a1-177">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="254a1-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="254a1-178">Quando você executar o aplicativo, verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="254a1-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="254a1-179">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="254a1-180">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="254a1-180">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="254a1-182">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="254a1-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="254a1-183">Próximo</span><span class="sxs-lookup"><span data-stu-id="254a1-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="254a1-184">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="254a1-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="254a1-185">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="254a1-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="254a1-186">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="254a1-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="254a1-187">Crie um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="254a1-187">Create a web app.</span></span>
> * <span data-ttu-id="254a1-188">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="254a1-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="254a1-189">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="254a1-189">Work with a database.</span></span>
> * <span data-ttu-id="254a1-190">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="254a1-190">Add search and validation.</span></span>

<span data-ttu-id="254a1-191">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="254a1-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="254a1-192">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="254a1-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254a1-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254a1-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="254a1-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254a1-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254a1-195">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="254a1-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="254a1-196">Criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="254a1-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254a1-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254a1-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="254a1-198">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="254a1-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="254a1-199">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="254a1-201">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="254a1-202">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="254a1-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="254a1-204">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="254a1-205">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="254a1-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="254a1-206">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="254a1-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="254a1-207">Você pode ter um aplicativo funcionando agora mesmo digitando um nome de projeto e selecionando algumas opções.</span><span class="sxs-lookup"><span data-stu-id="254a1-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="254a1-208">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="254a1-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="254a1-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254a1-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="254a1-210">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="254a1-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="254a1-211">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="254a1-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="254a1-212">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="254a1-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="254a1-213">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="254a1-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="254a1-214">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="254a1-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="254a1-215">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' MvcMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="254a1-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="254a1-216">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="254a1-216">Select **Yes**</span></span>

  * <span data-ttu-id="254a1-217">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="254a1-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="254a1-218">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie. csproj* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="254a1-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254a1-219">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="254a1-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="254a1-220">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="254a1-220">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="254a1-222">Selecione **.NET Core** > **aplicativo** > **aplicativo Web (Model-View-Controller)** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="254a1-224">Na caixa de diálogo **Configurar a nova API Web com o ASP.NET Core**, aceite a **Estrutura de Destino** padrão, que é o **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="254a1-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Seleção do .NET Core 2.2 do macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="254a1-226">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="254a1-227">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="254a1-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="254a1-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="254a1-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="254a1-229">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="254a1-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="254a1-230">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="254a1-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="254a1-231">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="254a1-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="254a1-232">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="254a1-233">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="254a1-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="254a1-234">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="254a1-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="254a1-235">Muitos desenvolvedores preferem usar o modo de não depuração para iniciar o aplicativo rapidamente e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="254a1-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="254a1-236">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="254a1-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="254a1-238">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="254a1-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="254a1-240">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="254a1-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="254a1-241">Este aplicativo não rastreia informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="254a1-241">This app doesn't track personal information.</span></span> <span data-ttu-id="254a1-242">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="254a1-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="254a1-244">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="254a1-244">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="254a1-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="254a1-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="254a1-247">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="254a1-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="254a1-248">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="254a1-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="254a1-249">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="254a1-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="254a1-250">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="254a1-251">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="254a1-252">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="254a1-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="254a1-253">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="254a1-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="254a1-254">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="254a1-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="254a1-255">Este aplicativo não rastreia informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="254a1-255">This app doesn't track personal information.</span></span> <span data-ttu-id="254a1-256">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="254a1-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="254a1-258">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="254a1-258">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="254a1-260">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="254a1-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="254a1-261">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="254a1-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="254a1-262">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="254a1-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="254a1-263">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="254a1-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="254a1-264">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="254a1-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="254a1-265">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="254a1-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="254a1-266">Quando você executar o aplicativo, verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="254a1-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="254a1-267">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="254a1-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="254a1-268">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="254a1-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="254a1-269">Este aplicativo não rastreia informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="254a1-269">This app doesn't track personal information.</span></span> <span data-ttu-id="254a1-270">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="254a1-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="254a1-272">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="254a1-272">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="254a1-274">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="254a1-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="254a1-275">Próximo</span><span class="sxs-lookup"><span data-stu-id="254a1-275">Next</span></span>](adding-controller.md)

::: moniker-end
