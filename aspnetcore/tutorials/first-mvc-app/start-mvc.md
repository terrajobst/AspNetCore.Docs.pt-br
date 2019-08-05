---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 04/24/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: b3544769b0e40fc27f5b6c939c6d7b5f9082f854
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682806"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="efd1d-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="efd1d-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="efd1d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="efd1d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="efd1d-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="efd1d-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="efd1d-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="efd1d-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="efd1d-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="efd1d-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="efd1d-108">Criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="efd1d-108">Create a web app.</span></span>
> * <span data-ttu-id="efd1d-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="efd1d-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="efd1d-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="efd1d-110">Work with a database.</span></span>
> * <span data-ttu-id="efd1d-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="efd1d-111">Add search and validation.</span></span>

<span data-ttu-id="efd1d-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="efd1d-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="efd1d-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="efd1d-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="efd1d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="efd1d-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="efd1d-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="efd1d-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="efd1d-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="efd1d-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="efd1d-117">Como criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="efd1d-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="efd1d-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="efd1d-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="efd1d-119">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="efd1d-120">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-120">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="efd1d-122">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="efd1d-123">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)

* <span data-ttu-id="efd1d-125">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="efd1d-126">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="efd1d-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="efd1d-127">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="efd1d-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="efd1d-128">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="efd1d-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="efd1d-129">Este é um projeto inicial básico.</span><span class="sxs-lookup"><span data-stu-id="efd1d-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="efd1d-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="efd1d-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="efd1d-131">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="efd1d-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="efd1d-132">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="efd1d-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="efd1d-133">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="efd1d-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="efd1d-134">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="efd1d-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="efd1d-135">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="efd1d-135">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="efd1d-136">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de 'MvcMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="efd1d-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="efd1d-137">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="efd1d-137">Select **Yes**</span></span>

  * <span data-ttu-id="efd1d-138">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="efd1d-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="efd1d-139">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie.csproj* no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="efd1d-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="efd1d-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="efd1d-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="efd1d-141">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-141">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="efd1d-143">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web (Model-View-Controller)** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="efd1d-145">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, defina a **Estrutura de Destino** do **.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.0**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="efd1d-146">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="efd1d-147">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="efd1d-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="efd1d-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="efd1d-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="efd1d-149">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="efd1d-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="efd1d-150">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="efd1d-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="efd1d-151">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="efd1d-152">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="efd1d-153">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="efd1d-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="efd1d-154">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="efd1d-155">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="efd1d-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="efd1d-156">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="efd1d-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="efd1d-158">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="efd1d-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)


  <span data-ttu-id="efd1d-160">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="efd1d-160">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="efd1d-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="efd1d-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="efd1d-163">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="efd1d-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="efd1d-164">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="efd1d-165">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="efd1d-166">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="efd1d-167">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="efd1d-168">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="efd1d-169">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="efd1d-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="efd1d-170">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="efd1d-170">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="efd1d-171">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="efd1d-171">This app doesn't track personal information.</span></span> <span data-ttu-id="efd1d-172">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="efd1d-172">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="efd1d-174">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="efd1d-174">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="efd1d-176">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="efd1d-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="efd1d-177">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="efd1d-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="efd1d-178">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="efd1d-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="efd1d-179">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="efd1d-180">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="efd1d-181">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="efd1d-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="efd1d-182">Quando você executar o aplicativo, verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="efd1d-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="efd1d-183">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="efd1d-184">A imagem a seguir mostra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="efd1d-184">The following image shows the app:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="efd1d-186">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="efd1d-187">Avançar</span><span class="sxs-lookup"><span data-stu-id="efd1d-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="efd1d-188">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="efd1d-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="efd1d-189">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="efd1d-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="efd1d-190">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="efd1d-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="efd1d-191">Criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="efd1d-191">Create a web app.</span></span>
> * <span data-ttu-id="efd1d-192">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="efd1d-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="efd1d-193">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="efd1d-193">Work with a database.</span></span>
> * <span data-ttu-id="efd1d-194">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="efd1d-194">Add search and validation.</span></span>

<span data-ttu-id="efd1d-195">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="efd1d-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="efd1d-196">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="efd1d-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="efd1d-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="efd1d-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="efd1d-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="efd1d-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="efd1d-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="efd1d-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="efd1d-200">Como criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="efd1d-200">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="efd1d-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="efd1d-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="efd1d-202">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="efd1d-203">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-203">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="efd1d-205">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="efd1d-206">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="efd1d-208">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="efd1d-209">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="efd1d-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="efd1d-210">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="efd1d-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="efd1d-211">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="efd1d-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="efd1d-212">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="efd1d-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="efd1d-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="efd1d-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="efd1d-214">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="efd1d-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="efd1d-215">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="efd1d-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="efd1d-216">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="efd1d-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="efd1d-217">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="efd1d-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="efd1d-218">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="efd1d-218">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="efd1d-219">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de 'MvcMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="efd1d-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="efd1d-220">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="efd1d-220">Select **Yes**</span></span>

  * <span data-ttu-id="efd1d-221">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="efd1d-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="efd1d-222">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie.csproj* no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="efd1d-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="efd1d-223">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="efd1d-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="efd1d-224">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-224">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="efd1d-226">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web (Model-View-Controller)** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-226">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="efd1d-228">Na caixa de diálogo **Configurar a nova API Web com o ASP.NET Core**, aceite a **Estrutura de Destino** padrão, que é o **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-228">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Seleção do .NET Core 2.2 do macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="efd1d-230">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-230">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="efd1d-231">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="efd1d-231">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="efd1d-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="efd1d-232">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="efd1d-233">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="efd1d-233">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="efd1d-234">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="efd1d-234">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="efd1d-235">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-235">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="efd1d-236">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-236">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="efd1d-237">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="efd1d-237">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="efd1d-238">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-238">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="efd1d-239">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="efd1d-239">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="efd1d-240">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="efd1d-240">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="efd1d-242">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="efd1d-242">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="efd1d-244">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="efd1d-244">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="efd1d-245">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="efd1d-245">This app doesn't track personal information.</span></span> <span data-ttu-id="efd1d-246">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="efd1d-246">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="efd1d-248">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="efd1d-248">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="efd1d-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="efd1d-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="efd1d-251">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="efd1d-251">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="efd1d-252">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-252">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="efd1d-253">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-253">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="efd1d-254">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-254">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="efd1d-255">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-255">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="efd1d-256">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-256">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="efd1d-257">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="efd1d-257">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="efd1d-258">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="efd1d-258">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="efd1d-259">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="efd1d-259">This app doesn't track personal information.</span></span> <span data-ttu-id="efd1d-260">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="efd1d-260">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="efd1d-262">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="efd1d-262">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="efd1d-264">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="efd1d-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="efd1d-265">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="efd1d-265">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="efd1d-266">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="efd1d-266">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="efd1d-267">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="efd1d-267">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="efd1d-268">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="efd1d-268">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="efd1d-269">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="efd1d-269">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="efd1d-270">Quando você executar o aplicativo, verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="efd1d-270">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="efd1d-271">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="efd1d-271">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="efd1d-272">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="efd1d-272">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="efd1d-273">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="efd1d-273">This app doesn't track personal information.</span></span> <span data-ttu-id="efd1d-274">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="efd1d-274">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="efd1d-276">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="efd1d-276">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="efd1d-278">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="efd1d-278">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="efd1d-279">Avançar</span><span class="sxs-lookup"><span data-stu-id="efd1d-279">Next</span></span>](adding-controller.md)

::: moniker-end
