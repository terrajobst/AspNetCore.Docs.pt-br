---
title: 'Tutorial: introdução ao Razor Pages no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722981"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="11388-104">Tutorial: introdução ao Razor Pages no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11388-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="11388-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="11388-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="11388-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11388-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="11388-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="11388-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="11388-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="11388-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="11388-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="11388-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="11388-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11388-110">Run the app.</span></span>
> * <span data-ttu-id="11388-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="11388-111">Examine the project files.</span></span>

<span data-ttu-id="11388-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="11388-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="11388-114">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="11388-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="11388-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11388-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="11388-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="11388-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="11388-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="11388-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="11388-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="11388-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="11388-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11388-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="11388-120">No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="11388-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="11388-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="11388-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="11388-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="11388-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="11388-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="11388-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="11388-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="11388-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="11388-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="11388-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="11388-126">Selecione **ASP.NET Core 3,1** no menu suspenso, **aplicativo Web**e, em seguida, selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="11388-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="11388-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="11388-128">The following starter project is created:</span></span>

  ![Gerenciador de soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="11388-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="11388-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="11388-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="11388-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="11388-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="11388-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="11388-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="11388-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="11388-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="11388-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="11388-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="11388-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="11388-136">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="11388-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="11388-137">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="11388-137">Select **Yes**.</span></span>

  <span data-ttu-id="11388-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="11388-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="11388-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="11388-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="11388-140">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="11388-140">Select **File** > **New Solution**.</span></span>

![Nova Solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="11388-142">Selecione **.NET Core** > **aplicativo** > **aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="11388-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="11388-144">Na caixa de diálogo **configurar seu novo aplicativo Web** , defina a **estrutura de destino** como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="11388-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![seleção do macOS .NET Core 3,1](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="11388-146">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="11388-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="11388-148">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="11388-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="11388-149">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="11388-149">Examine the project files</span></span>

<span data-ttu-id="11388-150">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="11388-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="11388-151">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="11388-151">Pages folder</span></span>

<span data-ttu-id="11388-152">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="11388-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="11388-153">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="11388-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="11388-154">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="11388-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="11388-155">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="11388-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="11388-156">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="11388-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="11388-157">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="11388-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="11388-158">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="11388-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="11388-159">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="11388-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="11388-160">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="11388-160">wwwroot folder</span></span>

<span data-ttu-id="11388-161">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="11388-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="11388-162">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="11388-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="11388-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="11388-163">appSettings.json</span></span>

<span data-ttu-id="11388-164">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="11388-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="11388-165">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="11388-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="11388-166">Module.vb</span><span class="sxs-lookup"><span data-stu-id="11388-166">Program.cs</span></span>

<span data-ttu-id="11388-167">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="11388-167">Contains the entry point for the program.</span></span> <span data-ttu-id="11388-168">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="11388-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="11388-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="11388-169">Startup.cs</span></span>

<span data-ttu-id="11388-170">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11388-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="11388-171">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="11388-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="11388-172">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="11388-172">Next steps</span></span>

<span data-ttu-id="11388-173">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="11388-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="11388-174">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="11388-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="11388-175">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="11388-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="11388-176">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="11388-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="11388-177">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="11388-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="11388-178">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="11388-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="11388-179">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="11388-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="11388-180">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11388-180">Run the app.</span></span>
> * <span data-ttu-id="11388-181">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="11388-181">Examine the project files.</span></span>

<span data-ttu-id="11388-182">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="11388-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="11388-184">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="11388-184">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="11388-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11388-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="11388-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="11388-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="11388-187">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="11388-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="11388-188">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="11388-188">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="11388-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11388-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="11388-190">No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="11388-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="11388-191">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="11388-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="11388-193">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="11388-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="11388-194">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="11388-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="11388-196">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="11388-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="11388-198">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="11388-198">The following starter project is created:</span></span>

  ![Gerenciador de soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="11388-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="11388-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="11388-201">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="11388-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="11388-202">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="11388-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="11388-203">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="11388-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="11388-204">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="11388-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="11388-205">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="11388-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="11388-206">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' RazorPagesMovie '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="11388-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="11388-207">Selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="11388-207">Select **Yes**.</span></span>

  <span data-ttu-id="11388-208">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="11388-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="11388-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="11388-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="11388-210">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="11388-210">Select **File** > **New Solution**.</span></span>

![Nova Solução do macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="11388-212">Selecione **.NET Core** > **aplicativo** > **aplicativo Web** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="11388-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="11388-214">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , defina a **estrutura de destino** como **.NET Core 3,1**.</span><span class="sxs-lookup"><span data-stu-id="11388-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Seleção do .NET Core 3.0 do macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="11388-216">Nomeie o projeto **RazorPagesMovie** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="11388-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="11388-218">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="11388-218">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="11388-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11388-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="11388-220">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="11388-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="11388-221">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="11388-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="11388-222">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="11388-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="11388-223">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="11388-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="11388-224">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="11388-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="11388-225">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="11388-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="11388-226">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="11388-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="11388-227">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="11388-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="11388-229">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="11388-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="11388-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="11388-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="11388-232">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="11388-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="11388-233">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="11388-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="11388-234">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="11388-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="11388-235">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="11388-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="11388-236">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="11388-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="11388-237">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="11388-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="11388-238">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="11388-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="11388-240">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="11388-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="11388-242">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="11388-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="11388-243">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="11388-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="11388-244">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="11388-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="11388-245">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="11388-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="11388-246">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="11388-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="11388-248">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="11388-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="11388-250">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="11388-250">Examine the project files</span></span>

<span data-ttu-id="11388-251">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="11388-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="11388-252">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="11388-252">Pages folder</span></span>

<span data-ttu-id="11388-253">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="11388-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="11388-254">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="11388-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="11388-255">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="11388-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="11388-256">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="11388-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="11388-257">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="11388-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="11388-258">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="11388-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="11388-259">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="11388-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="11388-260">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="11388-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="11388-261">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="11388-261">wwwroot folder</span></span>

<span data-ttu-id="11388-262">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="11388-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="11388-263">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="11388-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="11388-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="11388-264">appSettings.json</span></span>

<span data-ttu-id="11388-265">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="11388-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="11388-266">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="11388-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="11388-267">Module.vb</span><span class="sxs-lookup"><span data-stu-id="11388-267">Program.cs</span></span>

<span data-ttu-id="11388-268">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="11388-268">Contains the entry point for the program.</span></span> <span data-ttu-id="11388-269">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="11388-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="11388-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="11388-270">Startup.cs</span></span>

<span data-ttu-id="11388-271">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="11388-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="11388-272">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="11388-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11388-273">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="11388-273">Additional resources</span></span>

* [<span data-ttu-id="11388-274">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="11388-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="11388-275">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="11388-275">Next steps</span></span>

<span data-ttu-id="11388-276">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="11388-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="11388-277">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="11388-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
