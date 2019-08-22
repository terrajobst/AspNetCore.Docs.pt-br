---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 67a5fcee0a37861fd39a018443edbc0b9e513213
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487642"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="2a784-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a784-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="2a784-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2a784-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="2a784-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a784-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="2a784-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="2a784-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="2a784-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="2a784-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a784-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2a784-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2a784-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a784-110">Run the app.</span></span>
> * <span data-ttu-id="2a784-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="2a784-111">Examine the project files.</span></span>

<span data-ttu-id="2a784-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="2a784-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2a784-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="2a784-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a784-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a784-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2a784-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a784-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2a784-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a784-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="2a784-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="2a784-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a784-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a784-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a784-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="2a784-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2a784-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2a784-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="2a784-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="2a784-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="2a784-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="2a784-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2a784-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="2a784-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="2a784-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="2a784-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="2a784-126">Selecione **ASP.NET Core 3.0** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2a784-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="2a784-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="2a784-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2a784-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a784-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a784-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2a784-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2a784-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="2a784-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2a784-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="2a784-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2a784-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="2a784-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2a784-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2a784-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2a784-136">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="2a784-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2a784-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="2a784-137">Select **Yes**.</span></span>

  <span data-ttu-id="2a784-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="2a784-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2a784-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a784-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a784-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="2a784-140">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2a784-142">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2a784-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="2a784-144">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core**, defina a **Estrutura de Destino** como **.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="2a784-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="2a784-146">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2a784-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="2a784-148">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="2a784-148">Open the project</span></span>

<span data-ttu-id="2a784-149">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2a784-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2a784-150">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="2a784-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a784-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a784-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a784-152">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="2a784-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2a784-153">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a784-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2a784-154">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2a784-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2a784-155">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2a784-156">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2a784-157">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="2a784-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2a784-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a784-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2a784-159">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="2a784-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2a784-160">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2a784-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="2a784-161">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2a784-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2a784-162">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2a784-163">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2a784-164">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a784-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2a784-165">Pressione **Alt-Cmd-Enter** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="2a784-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="2a784-166">Como alternativa, navegue até a barra de menus e acesse Executar > Iniciar sem depuração.</span><span class="sxs-lookup"><span data-stu-id="2a784-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="2a784-167">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2a784-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="2a784-168">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="2a784-168">Examine the project files</span></span>

<span data-ttu-id="2a784-169">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="2a784-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2a784-170">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="2a784-170">Pages folder</span></span>

<span data-ttu-id="2a784-171">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="2a784-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2a784-172">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="2a784-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2a784-173">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="2a784-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2a784-174">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="2a784-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="2a784-175">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="2a784-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2a784-176">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="2a784-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2a784-177">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="2a784-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2a784-178">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2a784-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2a784-179">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="2a784-179">wwwroot folder</span></span>

<span data-ttu-id="2a784-180">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="2a784-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2a784-181">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2a784-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2a784-182">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="2a784-182">appSettings.json</span></span>

<span data-ttu-id="2a784-183">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="2a784-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="2a784-184">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2a784-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2a784-185">Module.vb</span><span class="sxs-lookup"><span data-stu-id="2a784-185">Program.cs</span></span>

<span data-ttu-id="2a784-186">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="2a784-186">Contains the entry point for the program.</span></span> <span data-ttu-id="2a784-187">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2a784-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2a784-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2a784-188">Startup.cs</span></span>

<span data-ttu-id="2a784-189">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a784-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="2a784-190">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="2a784-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2a784-191">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2a784-191">Next steps</span></span>

<span data-ttu-id="2a784-192">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="2a784-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2a784-193">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="2a784-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a784-194">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="2a784-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="2a784-195">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a784-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="2a784-196">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="2a784-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="2a784-197">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="2a784-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a784-198">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2a784-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2a784-199">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a784-199">Run the app.</span></span>
> * <span data-ttu-id="2a784-200">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="2a784-200">Examine the project files.</span></span>

<span data-ttu-id="2a784-201">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="2a784-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2a784-203">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="2a784-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a784-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a784-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2a784-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a784-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2a784-206">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a784-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="2a784-207">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="2a784-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a784-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a784-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a784-209">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="2a784-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="2a784-210">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="2a784-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="2a784-212">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="2a784-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2a784-213">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="2a784-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="2a784-215">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2a784-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="2a784-217">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="2a784-217">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2a784-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a784-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a784-220">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2a784-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2a784-221">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="2a784-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2a784-222">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="2a784-222">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2a784-223">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="2a784-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2a784-224">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2a784-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2a784-225">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="2a784-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2a784-226">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="2a784-226">Select **Yes**.</span></span>

  <span data-ttu-id="2a784-227">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="2a784-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2a784-228">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a784-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a784-229">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2a784-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="2a784-230">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2a784-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="2a784-231">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="2a784-231">Open the project</span></span>

<span data-ttu-id="2a784-232">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2a784-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2a784-233">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="2a784-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a784-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a784-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a784-235">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="2a784-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2a784-236">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2a784-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2a784-237">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2a784-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2a784-238">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2a784-239">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2a784-240">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="2a784-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="2a784-241">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="2a784-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2a784-242">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="2a784-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2a784-244">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="2a784-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2a784-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a784-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2a784-247">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="2a784-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2a784-248">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2a784-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="2a784-249">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="2a784-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2a784-250">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2a784-251">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="2a784-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="2a784-252">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="2a784-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2a784-253">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="2a784-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2a784-255">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="2a784-255">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2a784-257">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2a784-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2a784-258">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="2a784-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2a784-259">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2a784-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="2a784-260">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="2a784-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2a784-261">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="2a784-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="2a784-263">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="2a784-263">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="2a784-265">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="2a784-265">Examine the project files</span></span>

<span data-ttu-id="2a784-266">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="2a784-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2a784-267">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="2a784-267">Pages folder</span></span>

<span data-ttu-id="2a784-268">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="2a784-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2a784-269">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="2a784-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2a784-270">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="2a784-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2a784-271">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="2a784-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="2a784-272">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="2a784-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2a784-273">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="2a784-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2a784-274">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="2a784-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2a784-275">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="2a784-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2a784-276">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="2a784-276">wwwroot folder</span></span>

<span data-ttu-id="2a784-277">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="2a784-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2a784-278">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="2a784-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2a784-279">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="2a784-279">appSettings.json</span></span>

<span data-ttu-id="2a784-280">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="2a784-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="2a784-281">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2a784-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2a784-282">Module.vb</span><span class="sxs-lookup"><span data-stu-id="2a784-282">Program.cs</span></span>

<span data-ttu-id="2a784-283">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="2a784-283">Contains the entry point for the program.</span></span> <span data-ttu-id="2a784-284">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2a784-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2a784-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2a784-285">Startup.cs</span></span>

<span data-ttu-id="2a784-286">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="2a784-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="2a784-287">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="2a784-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a784-288">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2a784-288">Additional resources</span></span>

* [<span data-ttu-id="2a784-289">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="2a784-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="2a784-290">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2a784-290">Next steps</span></span>

<span data-ttu-id="2a784-291">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="2a784-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2a784-292">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="2a784-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
