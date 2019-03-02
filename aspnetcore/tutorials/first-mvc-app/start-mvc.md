---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 12/12/2018
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c09c06f55c4179e9e2174f0063ab7387b7e4c31b
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899223"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="56961-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="56961-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="56961-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56961-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="56961-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="56961-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="56961-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="56961-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="56961-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="56961-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56961-108">Criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="56961-108">Create a web app.</span></span>
> * <span data-ttu-id="56961-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="56961-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="56961-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="56961-110">Work with a database.</span></span>
> * <span data-ttu-id="56961-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="56961-111">Add search and validation.</span></span>

<span data-ttu-id="56961-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="56961-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="56961-113">Como criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="56961-113">Create a web app</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56961-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56961-114">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56961-115">No Visual Studio, selecione **Arquivo > Novo > Projeto**.</span><span class="sxs-lookup"><span data-stu-id="56961-115">From Visual Studio, select  **File > New > Project**.</span></span>

![Arquivo > Novo > Projeto](start-mvc/_static/alt_new_project.png)

<span data-ttu-id="56961-117">Complete a caixa de diálogo **Novo Projeto**:</span><span class="sxs-lookup"><span data-stu-id="56961-117">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="56961-118">No painel esquerdo, selecione **.NET Core**</span><span class="sxs-lookup"><span data-stu-id="56961-118">In the left pane, select **.NET Core**</span></span>
* <span data-ttu-id="56961-119">No painel central, selecione **Aplicativo Web ASP.NET Core (.NET Core)**</span><span class="sxs-lookup"><span data-stu-id="56961-119">In the center pane, select **ASP.NET Core Web Application (.NET Core)**</span></span>
* <span data-ttu-id="56961-120">Nomeie o projeto "MvcMovie" (é importante nomear o projeto "MvcMovie" para que, quando você copiar o código, o namespace corresponda).</span><span class="sxs-lookup"><span data-stu-id="56961-120">Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)</span></span>
* <span data-ttu-id="56961-121">Selecione **OK**</span><span class="sxs-lookup"><span data-stu-id="56961-121">select **OK**</span></span>

![<span data-ttu-id="56961-122">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56961-122">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project2-21.png)

<span data-ttu-id="56961-123">Faça as configurações necessárias na caixa de diálogo **Novo aplicativo Web ASP.NET Core (.NET Core) – MvcMovie**:</span><span class="sxs-lookup"><span data-stu-id="56961-123">Complete the **New ASP.NET Core Web Application (.NET Core) - MvcMovie** dialog:</span></span>

* <span data-ttu-id="56961-124">Na caixa de lista suspensa do seletor de versão, selecione **ASP.NET Core 2.2**</span><span class="sxs-lookup"><span data-stu-id="56961-124">In the version selector drop-down box select **ASP.NET Core 2.2**</span></span>
* <span data-ttu-id="56961-125">Selecione **Aplicativo Web (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="56961-125">Select **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="56961-126">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="56961-126">select **OK**.</span></span>

![<span data-ttu-id="56961-127">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56961-127">New project dialog, .Net core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="56961-128">O Visual Studio usou um modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="56961-128">Visual Studio used a default template for the MVC project you just created.</span></span> <span data-ttu-id="56961-129">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="56961-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="56961-130">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="56961-130">This is a basic starter project, and it's a good place to start.</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56961-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56961-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56961-132">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="56961-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="56961-133">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="56961-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="56961-134">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="56961-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="56961-135">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="56961-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="56961-136">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="56961-136">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="56961-137">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de 'MvcMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="56961-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="56961-138">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="56961-138">Select **Yes**</span></span>

  * <span data-ttu-id="56961-139">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="56961-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="56961-140">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie.csproj* no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="56961-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56961-141">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="56961-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="56961-142">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="56961-142">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](~/tutorials/first-web-api-mac/_static/sln.png)

* <span data-ttu-id="56961-144">Selecione **Aplicativo .NET Core** > **ASP.NET Core** > **Aplicativo Web ASP.NET Core (MVC)** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="56961-144">Select **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](~/tutorials/first-mvc-app-mac/start-mvc/1.png)

* <span data-ttu-id="56961-146">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="56961-146">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="56961-147">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="56961-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---  
<!-- End of VS tabs -->

### <a name="run-the-app"></a><span data-ttu-id="56961-148">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="56961-148">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56961-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56961-149">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="56961-150">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="56961-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="56961-151">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="56961-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="56961-152">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="56961-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="56961-153">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="56961-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="56961-154">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="56961-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="56961-155">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="56961-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="56961-156">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="56961-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="56961-157">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="56961-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="56961-159">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="56961-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56961-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56961-161">Visual Studio Code</span></span>](#tab/visual-studio-code) 

<span data-ttu-id="56961-162">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="56961-162">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="56961-163">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="56961-163">Visual Studio Code starts starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="56961-164">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="56961-164">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="56961-165">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="56961-165">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="56961-166">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="56961-166">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="56961-167">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="56961-167">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="56961-168">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="56961-168">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56961-169">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="56961-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56961-170">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="56961-170">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="56961-171">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="56961-171">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="56961-172">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="56961-172">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="56961-173">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="56961-173">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="56961-174">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="56961-174">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="56961-175">Quando você executar o aplicativo, verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="56961-175">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="56961-176">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="56961-176">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

------

* <span data-ttu-id="56961-177">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="56961-177">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="56961-178">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="56961-178">This app doesn't track personal information.</span></span> <span data-ttu-id="56961-179">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="56961-179">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="56961-181">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="56961-181">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="56961-183">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="56961-183">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="56961-184">Avançar</span><span class="sxs-lookup"><span data-stu-id="56961-184">Next</span></span>](adding-controller.md)  
