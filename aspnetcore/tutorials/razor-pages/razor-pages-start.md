---
title: 'Tutorial: introdução ao Razor Pages no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: b651437b698d01310f90c5f14832616c1896e6c0
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74959093"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="02cf1-104">Tutorial: introdução ao Razor Pages no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02cf1-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="02cf1-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02cf1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="02cf1-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02cf1-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="02cf1-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="02cf1-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="02cf1-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="02cf1-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02cf1-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="02cf1-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="02cf1-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02cf1-110">Run the app.</span></span>
> * <span data-ttu-id="02cf1-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="02cf1-111">Examine the project files.</span></span>

<span data-ttu-id="02cf1-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="02cf1-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="02cf1-114">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="02cf1-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02cf1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02cf1-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02cf1-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02cf1-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02cf1-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="02cf1-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="02cf1-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="02cf1-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02cf1-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02cf1-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02cf1-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="02cf1-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="02cf1-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="02cf1-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="02cf1-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="02cf1-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="02cf1-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="02cf1-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="02cf1-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="02cf1-126">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web**e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="02cf1-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="02cf1-128">The following starter project is created:</span></span>

  ![Gerenciador de soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02cf1-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02cf1-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="02cf1-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="02cf1-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="02cf1-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="02cf1-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="02cf1-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="02cf1-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="02cf1-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="02cf1-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="02cf1-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="02cf1-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="02cf1-136">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="02cf1-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="02cf1-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="02cf1-137">Select **Yes**.</span></span>

  <span data-ttu-id="02cf1-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="02cf1-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02cf1-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="02cf1-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02cf1-140">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-140">Select **File** > **New Solution**.</span></span>

![Nova Solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="02cf1-142">Selecione **.NET Core** > **Aplicativo** > **Aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="02cf1-144">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="02cf1-146">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="02cf1-148">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="02cf1-148">Open the project</span></span>

<span data-ttu-id="02cf1-149">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="02cf1-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="02cf1-150">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="02cf1-150">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="02cf1-151">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="02cf1-151">Examine the project files</span></span>

<span data-ttu-id="02cf1-152">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="02cf1-152">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="02cf1-153">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="02cf1-153">Pages folder</span></span>

<span data-ttu-id="02cf1-154">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="02cf1-154">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="02cf1-155">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="02cf1-155">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="02cf1-156">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="02cf1-156">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="02cf1-157">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="02cf1-157">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="02cf1-158">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="02cf1-158">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="02cf1-159">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="02cf1-159">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="02cf1-160">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="02cf1-160">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="02cf1-161">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-161">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="02cf1-162">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="02cf1-162">wwwroot folder</span></span>

<span data-ttu-id="02cf1-163">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="02cf1-163">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="02cf1-164">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-164">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="02cf1-165">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="02cf1-165">appSettings.json</span></span>

<span data-ttu-id="02cf1-166">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="02cf1-166">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="02cf1-167">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-167">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="02cf1-168">Module.vb</span><span class="sxs-lookup"><span data-stu-id="02cf1-168">Program.cs</span></span>

<span data-ttu-id="02cf1-169">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="02cf1-169">Contains the entry point for the program.</span></span> <span data-ttu-id="02cf1-170">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="02cf1-171">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="02cf1-171">Startup.cs</span></span>

<span data-ttu-id="02cf1-172">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02cf1-172">Contains code that configures app behavior.</span></span> <span data-ttu-id="02cf1-173">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-173">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="02cf1-174">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="02cf1-174">Next steps</span></span>

<span data-ttu-id="02cf1-175">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="02cf1-175">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="02cf1-176">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="02cf1-176">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02cf1-177">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="02cf1-177">This is the first tutorial of a series.</span></span> <span data-ttu-id="02cf1-178">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="02cf1-178">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="02cf1-179">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="02cf1-179">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="02cf1-180">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="02cf1-180">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02cf1-181">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="02cf1-181">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="02cf1-182">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02cf1-182">Run the app.</span></span>
> * <span data-ttu-id="02cf1-183">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="02cf1-183">Examine the project files.</span></span>

<span data-ttu-id="02cf1-184">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="02cf1-184">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="02cf1-186">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="02cf1-186">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02cf1-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02cf1-187">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02cf1-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02cf1-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02cf1-189">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="02cf1-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="02cf1-190">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="02cf1-190">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02cf1-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02cf1-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02cf1-192">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="02cf1-193">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-193">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="02cf1-195">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-195">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="02cf1-196">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="02cf1-196">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="02cf1-198">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="02cf1-198">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="02cf1-200">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="02cf1-200">The following starter project is created:</span></span>

  ![Gerenciador de soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02cf1-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02cf1-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="02cf1-203">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="02cf1-203">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="02cf1-204">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="02cf1-204">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="02cf1-205">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="02cf1-205">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="02cf1-206">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="02cf1-206">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="02cf1-207">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="02cf1-207">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="02cf1-208">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="02cf1-208">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="02cf1-209">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="02cf1-209">Select **Yes**.</span></span>

  <span data-ttu-id="02cf1-210">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="02cf1-210">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02cf1-211">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="02cf1-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02cf1-212">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="02cf1-212">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="02cf1-213">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="02cf1-213">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="02cf1-214">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="02cf1-214">Open the project</span></span>

<span data-ttu-id="02cf1-215">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="02cf1-215">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="02cf1-216">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="02cf1-216">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02cf1-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02cf1-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02cf1-218">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="02cf1-218">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="02cf1-219">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02cf1-219">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="02cf1-220">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="02cf1-220">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02cf1-221">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="02cf1-221">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="02cf1-222">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="02cf1-222">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="02cf1-223">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="02cf1-223">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="02cf1-224">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="02cf1-224">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="02cf1-225">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="02cf1-225">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="02cf1-227">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="02cf1-227">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="02cf1-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02cf1-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="02cf1-230">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="02cf1-230">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="02cf1-231">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="02cf1-231">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="02cf1-232">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="02cf1-232">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="02cf1-233">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="02cf1-233">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="02cf1-234">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="02cf1-234">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="02cf1-235">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="02cf1-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="02cf1-236">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="02cf1-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="02cf1-238">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="02cf1-238">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="02cf1-240">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="02cf1-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="02cf1-241">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="02cf1-241">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="02cf1-242">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="02cf1-242">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="02cf1-243">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="02cf1-243">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="02cf1-244">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="02cf1-244">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="02cf1-246">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="02cf1-246">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="02cf1-248">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="02cf1-248">Examine the project files</span></span>

<span data-ttu-id="02cf1-249">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="02cf1-249">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="02cf1-250">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="02cf1-250">Pages folder</span></span>

<span data-ttu-id="02cf1-251">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="02cf1-251">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="02cf1-252">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="02cf1-252">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="02cf1-253">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="02cf1-253">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="02cf1-254">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="02cf1-254">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="02cf1-255">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="02cf1-255">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="02cf1-256">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="02cf1-256">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="02cf1-257">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="02cf1-257">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="02cf1-258">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-258">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="02cf1-259">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="02cf1-259">wwwroot folder</span></span>

<span data-ttu-id="02cf1-260">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="02cf1-260">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="02cf1-261">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-261">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="02cf1-262">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="02cf1-262">appSettings.json</span></span>

<span data-ttu-id="02cf1-263">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="02cf1-263">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="02cf1-264">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-264">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="02cf1-265">Module.vb</span><span class="sxs-lookup"><span data-stu-id="02cf1-265">Program.cs</span></span>

<span data-ttu-id="02cf1-266">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="02cf1-266">Contains the entry point for the program.</span></span> <span data-ttu-id="02cf1-267">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-267">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="02cf1-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="02cf1-268">Startup.cs</span></span>

<span data-ttu-id="02cf1-269">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="02cf1-269">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="02cf1-270">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="02cf1-270">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02cf1-271">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="02cf1-271">Additional resources</span></span>

* [<span data-ttu-id="02cf1-272">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="02cf1-272">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="02cf1-273">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="02cf1-273">Next steps</span></span>

<span data-ttu-id="02cf1-274">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="02cf1-274">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="02cf1-275">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="02cf1-275">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
