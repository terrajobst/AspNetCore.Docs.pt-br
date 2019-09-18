---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 0cc00cb85b6054752417b82c783cfd4c306aeda5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082570"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="eba85-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eba85-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="eba85-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eba85-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="eba85-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eba85-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="eba85-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="eba85-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="eba85-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="eba85-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eba85-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="eba85-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="eba85-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eba85-110">Run the app.</span></span>
> * <span data-ttu-id="eba85-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="eba85-111">Examine the project files.</span></span>

<span data-ttu-id="eba85-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="eba85-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="eba85-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="eba85-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eba85-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eba85-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eba85-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eba85-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eba85-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eba85-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="eba85-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="eba85-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eba85-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eba85-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eba85-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="eba85-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="eba85-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="eba85-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="eba85-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="eba85-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="eba85-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="eba85-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="eba85-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="eba85-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="eba85-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="eba85-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="eba85-126">Selecione **ASP.NET Core 3.0** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="eba85-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="eba85-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="eba85-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eba85-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eba85-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eba85-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="eba85-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="eba85-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="eba85-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="eba85-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="eba85-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="eba85-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="eba85-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="eba85-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eba85-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="eba85-136">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="eba85-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="eba85-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="eba85-137">Select **Yes**.</span></span>

  <span data-ttu-id="eba85-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="eba85-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eba85-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eba85-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eba85-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="eba85-140">Select **File** > **New Solution**.</span></span>

![Nova solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="eba85-142">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="eba85-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="eba85-144">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core**, defina a **Estrutura de Destino** como **.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="eba85-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="eba85-146">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="eba85-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="eba85-148">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="eba85-148">Open the project</span></span>

<span data-ttu-id="eba85-149">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="eba85-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="eba85-150">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="eba85-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eba85-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eba85-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eba85-152">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="eba85-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="eba85-153">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eba85-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="eba85-154">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eba85-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eba85-155">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="eba85-156">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="eba85-157">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="eba85-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eba85-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eba85-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="eba85-159">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="eba85-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="eba85-160">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eba85-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="eba85-161">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eba85-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eba85-162">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="eba85-163">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eba85-164">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eba85-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="eba85-165">Pressione **Alt-Cmd-Enter** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="eba85-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="eba85-166">Como alternativa, navegue até a barra de menus e acesse Executar > Iniciar sem depuração.</span><span class="sxs-lookup"><span data-stu-id="eba85-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="eba85-167">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eba85-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="eba85-168">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="eba85-168">Examine the project files</span></span>

<span data-ttu-id="eba85-169">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="eba85-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="eba85-170">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="eba85-170">Pages folder</span></span>

<span data-ttu-id="eba85-171">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="eba85-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="eba85-172">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="eba85-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="eba85-173">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="eba85-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="eba85-174">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="eba85-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="eba85-175">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="eba85-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="eba85-176">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="eba85-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="eba85-177">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="eba85-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="eba85-178">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="eba85-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="eba85-179">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="eba85-179">wwwroot folder</span></span>

<span data-ttu-id="eba85-180">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="eba85-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="eba85-181">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="eba85-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="eba85-182">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="eba85-182">appSettings.json</span></span>

<span data-ttu-id="eba85-183">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="eba85-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="eba85-184">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="eba85-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="eba85-185">Module.vb</span><span class="sxs-lookup"><span data-stu-id="eba85-185">Program.cs</span></span>

<span data-ttu-id="eba85-186">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="eba85-186">Contains the entry point for the program.</span></span> <span data-ttu-id="eba85-187">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="eba85-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="eba85-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="eba85-188">Startup.cs</span></span>

<span data-ttu-id="eba85-189">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eba85-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="eba85-190">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="eba85-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="eba85-191">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="eba85-191">Next steps</span></span>

<span data-ttu-id="eba85-192">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="eba85-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="eba85-193">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="eba85-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eba85-194">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="eba85-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="eba85-195">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eba85-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="eba85-196">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="eba85-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="eba85-197">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="eba85-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eba85-198">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="eba85-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="eba85-199">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eba85-199">Run the app.</span></span>
> * <span data-ttu-id="eba85-200">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="eba85-200">Examine the project files.</span></span>

<span data-ttu-id="eba85-201">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="eba85-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="eba85-203">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="eba85-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eba85-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eba85-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eba85-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eba85-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eba85-206">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eba85-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="eba85-207">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="eba85-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eba85-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eba85-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eba85-209">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="eba85-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="eba85-210">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="eba85-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="eba85-212">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="eba85-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="eba85-213">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="eba85-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="eba85-215">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="eba85-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="eba85-217">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="eba85-217">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eba85-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eba85-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eba85-220">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="eba85-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="eba85-221">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="eba85-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="eba85-222">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="eba85-222">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="eba85-223">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="eba85-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="eba85-224">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eba85-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="eba85-225">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="eba85-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="eba85-226">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="eba85-226">Select **Yes**.</span></span>

  <span data-ttu-id="eba85-227">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="eba85-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eba85-228">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eba85-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eba85-229">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="eba85-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="eba85-230">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="eba85-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="eba85-231">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="eba85-231">Open the project</span></span>

<span data-ttu-id="eba85-232">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="eba85-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="eba85-233">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="eba85-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eba85-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eba85-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eba85-235">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="eba85-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="eba85-236">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eba85-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="eba85-237">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eba85-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eba85-238">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="eba85-239">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="eba85-240">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="eba85-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="eba85-241">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="eba85-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="eba85-242">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="eba85-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="eba85-244">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="eba85-244">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="eba85-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eba85-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="eba85-247">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="eba85-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="eba85-248">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eba85-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="eba85-249">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eba85-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eba85-250">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="eba85-251">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="eba85-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="eba85-252">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="eba85-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="eba85-253">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="eba85-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="eba85-255">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="eba85-255">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="eba85-257">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eba85-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="eba85-258">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="eba85-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="eba85-259">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eba85-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="eba85-260">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="eba85-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="eba85-261">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="eba85-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="eba85-263">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="eba85-263">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="eba85-265">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="eba85-265">Examine the project files</span></span>

<span data-ttu-id="eba85-266">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="eba85-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="eba85-267">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="eba85-267">Pages folder</span></span>

<span data-ttu-id="eba85-268">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="eba85-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="eba85-269">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="eba85-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="eba85-270">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="eba85-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="eba85-271">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="eba85-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="eba85-272">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="eba85-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="eba85-273">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="eba85-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="eba85-274">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="eba85-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="eba85-275">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="eba85-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="eba85-276">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="eba85-276">wwwroot folder</span></span>

<span data-ttu-id="eba85-277">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="eba85-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="eba85-278">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="eba85-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="eba85-279">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="eba85-279">appSettings.json</span></span>

<span data-ttu-id="eba85-280">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="eba85-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="eba85-281">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="eba85-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="eba85-282">Module.vb</span><span class="sxs-lookup"><span data-stu-id="eba85-282">Program.cs</span></span>

<span data-ttu-id="eba85-283">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="eba85-283">Contains the entry point for the program.</span></span> <span data-ttu-id="eba85-284">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="eba85-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="eba85-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="eba85-285">Startup.cs</span></span>

<span data-ttu-id="eba85-286">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="eba85-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="eba85-287">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="eba85-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eba85-288">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="eba85-288">Additional resources</span></span>

* [<span data-ttu-id="eba85-289">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="eba85-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="eba85-290">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="eba85-290">Next steps</span></span>

<span data-ttu-id="eba85-291">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="eba85-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="eba85-292">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="eba85-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
