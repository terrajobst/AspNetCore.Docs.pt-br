---
title: Introdução ao ASP.NET Core MVC
author: rick-anderson
description: Saiba como começar a usar o ASP.NET Core MVC.
ms.author: riande
ms.date: 04/24/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: dc3499c89860190b76d6be7b8abeeaef827880d6
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491255"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="3848a-103">Introdução ao ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="3848a-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="3848a-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3848a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="3848a-105">Este tutorial ensina as noções básicas de criação de um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="3848a-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="3848a-106">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="3848a-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="3848a-107">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="3848a-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3848a-108">Criar um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="3848a-108">Create a web app.</span></span>
> * <span data-ttu-id="3848a-109">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="3848a-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="3848a-110">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3848a-110">Work with a database.</span></span>
> * <span data-ttu-id="3848a-111">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="3848a-111">Add search and validation.</span></span>

<span data-ttu-id="3848a-112">No final, você terá um aplicativo que pode gerenciar e exibir dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="3848a-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a><span data-ttu-id="3848a-113">Como criar um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="3848a-113">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3848a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3848a-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3848a-115">No Visual Studio, selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="3848a-115">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="3848a-116">Selecione **Aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3848a-116">Selecct **ASP.NET Core Web Application** and then select **Next**.</span></span>

![novo Aplicativo Web ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="3848a-118">Nomeie o projeto como **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3848a-118">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="3848a-119">É importante nomear o projeto como **MvcMovie** para corresponder ao namespace quando você copiar o código.</span><span class="sxs-lookup"><span data-stu-id="3848a-119">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![novo Aplicativo Web ASP.NET Core](start-mvc/_static/config.png)


* <span data-ttu-id="3848a-121">Selecione **Aplicativo Web (Model-View-Controller)** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3848a-121">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="3848a-122">Caixa de diálogo Novo projeto, .NET Core no painel esquerdo, Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3848a-122">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="3848a-123">O Visual Studio usou o modelo padrão para o projeto MVC que você acabou de criar.</span><span class="sxs-lookup"><span data-stu-id="3848a-123">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="3848a-124">Para que o aplicativo comece a funcionar agora mesmo, digite um nome de projeto e selecione algumas opções.</span><span class="sxs-lookup"><span data-stu-id="3848a-124">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="3848a-125">Este é um projeto inicial básico e é um bom ponto de partida.</span><span class="sxs-lookup"><span data-stu-id="3848a-125">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3848a-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3848a-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3848a-127">O tutorial pressupõe que você já tenha familiaridade com o VS Code.</span><span class="sxs-lookup"><span data-stu-id="3848a-127">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="3848a-128">Consulte [Introdução ao VS Code](https://code.visualstudio.com/docs) e [Ajuda do Visual Studio Code](#visual-studio-code-help) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="3848a-128">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="3848a-129">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3848a-129">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3848a-130">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="3848a-130">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="3848a-131">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3848a-131">Run the following command:</span></span>

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="3848a-132">Uma caixa de diálogo é exibida com **Os ativos necessários para build e depuração estão ausentes de 'MvcMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="3848a-132">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="3848a-133">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="3848a-133">Select **Yes**</span></span>

  * <span data-ttu-id="3848a-134">`dotnet new mvc -o MvcMovie`: cria um novo projeto do ASP.NET Core MVC na pasta *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="3848a-134">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="3848a-135">`code -r MvcMovie`: carrega o arquivo de projeto *MvcMovie.csproj* no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3848a-135">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3848a-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3848a-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3848a-137">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="3848a-137">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="3848a-139">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web (Model-View-Controller)**  > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3848a-139">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="3848a-141">Na caixa de diálogo **Configurar a nova API Web com o ASP.NET Core**, aceite a **Estrutura de Destino** padrão, que é o **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="3848a-141">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![Seleção do .NET Core 2.2 do macOS](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="3848a-143">Nomeie o projeto **MvcMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3848a-143">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="3848a-144">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3848a-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3848a-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3848a-145">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3848a-146">Pressione **Ctrl+F5** para executar o aplicativo no modo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="3848a-146">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="3848a-147">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3848a-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3848a-148">Observe que a barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3848a-148">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3848a-149">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3848a-149">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3848a-150">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="3848a-150">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="3848a-151">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="3848a-151">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3848a-152">Muitos desenvolvedores preferem usar modo de não depuração para iniciar o aplicativo e exibir alterações rapidamente.</span><span class="sxs-lookup"><span data-stu-id="3848a-152">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="3848a-153">Você pode iniciar o aplicativo no modo de não depuração ou de depuração por meio do item de menu **Depurar**:</span><span class="sxs-lookup"><span data-stu-id="3848a-153">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Menu Depurar](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="3848a-155">Você pode depurar o aplicativo selecionando o botão **IIS Express**</span><span class="sxs-lookup"><span data-stu-id="3848a-155">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="3848a-157">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="3848a-157">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3848a-158">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="3848a-158">This app doesn't track personal information.</span></span> <span data-ttu-id="3848a-159">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3848a-159">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="3848a-161">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="3848a-161">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3848a-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3848a-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3848a-164">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3848a-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="3848a-165">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3848a-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="3848a-166">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3848a-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="3848a-167">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3848a-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3848a-168">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="3848a-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="3848a-169">Iniciar o aplicativo com Ctrl+F5 (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="3848a-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="3848a-170">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="3848a-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="3848a-171">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="3848a-171">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3848a-172">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="3848a-172">This app doesn't track personal information.</span></span> <span data-ttu-id="3848a-173">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3848a-173">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/privacy.png)

  <span data-ttu-id="3848a-175">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="3848a-175">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3848a-177">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3848a-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3848a-178">Selecione **Executar** > **Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3848a-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3848a-179">O Visual Studio para Mac inicia o servidor [Kestrel](xref:fundamentals/servers/index#kestrel), inicia um navegador e navega para `http://localhost:port`, em que *porta* é um número da porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="3848a-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3848a-180">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3848a-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3848a-181">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3848a-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="3848a-182">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="3848a-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3848a-183">Quando você executar o aplicativo, verá um número da porta diferente.</span><span class="sxs-lookup"><span data-stu-id="3848a-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="3848a-184">Você pode iniciar o aplicativo no modo de depuração ou sem depuração no item de menu **Executar**.</span><span class="sxs-lookup"><span data-stu-id="3848a-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="3848a-185">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="3848a-185">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="3848a-186">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="3848a-186">This app doesn't track personal information.</span></span> <span data-ttu-id="3848a-187">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="3848a-187">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="3848a-189">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="3848a-189">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="3848a-191">Na próxima parte deste tutorial, você saberá mais sobre o MVC e começará a escrever um pouco de código.</span><span class="sxs-lookup"><span data-stu-id="3848a-191">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3848a-192">Avançar</span><span class="sxs-lookup"><span data-stu-id="3848a-192">Next</span></span>](adding-controller.md)
