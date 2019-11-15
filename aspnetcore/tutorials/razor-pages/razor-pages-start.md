---
title: 'Tutorial: introdução ao Razor Pages no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: a8381dee05f267077a29999f3d8bbe6327c2b863
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116155"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="8a741-104">Tutorial: introdução ao Razor Pages no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a741-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="8a741-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8a741-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="8a741-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a741-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="8a741-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="8a741-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="8a741-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="8a741-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8a741-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="8a741-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="8a741-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a741-110">Run the app.</span></span>
> * <span data-ttu-id="8a741-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="8a741-111">Examine the project files.</span></span>

<span data-ttu-id="8a741-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a741-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="8a741-114">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="8a741-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8a741-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a741-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8a741-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a741-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8a741-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8a741-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="8a741-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="8a741-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8a741-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a741-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8a741-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="8a741-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8a741-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="8a741-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="8a741-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="8a741-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="8a741-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="8a741-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="8a741-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="8a741-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="8a741-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="8a741-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="8a741-126">Selecione **ASP.NET Core 3.0** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="8a741-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="8a741-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="8a741-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8a741-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a741-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8a741-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8a741-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="8a741-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="8a741-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="8a741-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="8a741-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="8a741-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="8a741-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="8a741-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8a741-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="8a741-136">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="8a741-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="8a741-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="8a741-137">Select **Yes**.</span></span>

  <span data-ttu-id="8a741-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="8a741-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8a741-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8a741-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8a741-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="8a741-140">Select **File** > **New Solution**.</span></span>

![Nova Solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8a741-142">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="8a741-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="8a741-144">Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core**, defina a **Estrutura de Destino** como **.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="8a741-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="8a741-146">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="8a741-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="8a741-148">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="8a741-148">Open the project</span></span>

<span data-ttu-id="8a741-149">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="8a741-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="8a741-150">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="8a741-150">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="8a741-151">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="8a741-151">Examine the project files</span></span>

<span data-ttu-id="8a741-152">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a741-152">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="8a741-153">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="8a741-153">Pages folder</span></span>

<span data-ttu-id="8a741-154">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="8a741-154">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="8a741-155">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="8a741-155">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="8a741-156">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="8a741-156">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="8a741-157">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="8a741-157">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="8a741-158">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="8a741-158">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="8a741-159">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="8a741-159">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="8a741-160">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="8a741-160">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="8a741-161">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="8a741-161">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="8a741-162">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="8a741-162">wwwroot folder</span></span>

<span data-ttu-id="8a741-163">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="8a741-163">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="8a741-164">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="8a741-164">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="8a741-165">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="8a741-165">appSettings.json</span></span>

<span data-ttu-id="8a741-166">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="8a741-166">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="8a741-167">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8a741-167">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="8a741-168">Module.vb</span><span class="sxs-lookup"><span data-stu-id="8a741-168">Program.cs</span></span>

<span data-ttu-id="8a741-169">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="8a741-169">Contains the entry point for the program.</span></span> <span data-ttu-id="8a741-170">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="8a741-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="8a741-171">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="8a741-171">Startup.cs</span></span>

<span data-ttu-id="8a741-172">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a741-172">Contains code that configures app behavior.</span></span> <span data-ttu-id="8a741-173">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8a741-173">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8a741-174">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="8a741-174">Next steps</span></span>

<span data-ttu-id="8a741-175">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="8a741-175">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8a741-176">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="8a741-176">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8a741-177">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="8a741-177">This is the first tutorial of a series.</span></span> <span data-ttu-id="8a741-178">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a741-178">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="8a741-179">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="8a741-179">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="8a741-180">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="8a741-180">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8a741-181">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="8a741-181">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="8a741-182">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a741-182">Run the app.</span></span>
> * <span data-ttu-id="8a741-183">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="8a741-183">Examine the project files.</span></span>

<span data-ttu-id="8a741-184">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a741-184">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="8a741-186">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="8a741-186">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8a741-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a741-187">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8a741-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a741-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8a741-189">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8a741-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="8a741-190">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="8a741-190">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8a741-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a741-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8a741-192">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="8a741-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="8a741-193">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="8a741-193">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="8a741-195">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="8a741-195">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="8a741-196">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="8a741-196">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="8a741-198">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="8a741-198">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="8a741-200">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="8a741-200">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8a741-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a741-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8a741-203">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="8a741-203">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="8a741-204">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="8a741-204">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="8a741-205">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="8a741-205">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="8a741-206">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="8a741-206">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="8a741-207">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8a741-207">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="8a741-208">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="8a741-208">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="8a741-209">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="8a741-209">Select **Yes**.</span></span>

  <span data-ttu-id="8a741-210">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="8a741-210">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8a741-211">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8a741-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8a741-212">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8a741-212">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="8a741-213">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="8a741-213">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="8a741-214">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="8a741-214">Open the project</span></span>

<span data-ttu-id="8a741-215">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="8a741-215">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="8a741-216">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="8a741-216">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8a741-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a741-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8a741-218">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="8a741-218">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="8a741-219">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a741-219">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8a741-220">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8a741-220">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8a741-221">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8a741-221">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="8a741-222">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="8a741-222">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="8a741-223">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="8a741-223">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="8a741-224">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="8a741-224">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="8a741-225">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="8a741-225">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="8a741-227">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="8a741-227">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8a741-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8a741-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="8a741-230">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="8a741-230">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="8a741-231">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8a741-231">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="8a741-232">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="8a741-232">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8a741-233">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="8a741-233">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="8a741-234">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="8a741-234">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="8a741-235">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="8a741-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="8a741-236">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="8a741-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="8a741-238">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="8a741-238">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8a741-240">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8a741-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8a741-241">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="8a741-241">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="8a741-242">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8a741-242">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="8a741-243">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="8a741-243">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="8a741-244">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="8a741-244">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="8a741-246">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="8a741-246">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="8a741-248">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="8a741-248">Examine the project files</span></span>

<span data-ttu-id="8a741-249">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="8a741-249">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="8a741-250">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="8a741-250">Pages folder</span></span>

<span data-ttu-id="8a741-251">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="8a741-251">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="8a741-252">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="8a741-252">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="8a741-253">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="8a741-253">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="8a741-254">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="8a741-254">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="8a741-255">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="8a741-255">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="8a741-256">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="8a741-256">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="8a741-257">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="8a741-257">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="8a741-258">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="8a741-258">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="8a741-259">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="8a741-259">wwwroot folder</span></span>

<span data-ttu-id="8a741-260">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="8a741-260">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="8a741-261">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="8a741-261">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="8a741-262">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="8a741-262">appSettings.json</span></span>

<span data-ttu-id="8a741-263">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="8a741-263">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="8a741-264">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8a741-264">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="8a741-265">Module.vb</span><span class="sxs-lookup"><span data-stu-id="8a741-265">Program.cs</span></span>

<span data-ttu-id="8a741-266">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="8a741-266">Contains the entry point for the program.</span></span> <span data-ttu-id="8a741-267">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="8a741-267">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="8a741-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="8a741-268">Startup.cs</span></span>

<span data-ttu-id="8a741-269">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="8a741-269">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="8a741-270">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8a741-270">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a741-271">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8a741-271">Additional resources</span></span>

* [<span data-ttu-id="8a741-272">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="8a741-272">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="8a741-273">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="8a741-273">Next steps</span></span>

<span data-ttu-id="8a741-274">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="8a741-274">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8a741-275">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="8a741-275">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
