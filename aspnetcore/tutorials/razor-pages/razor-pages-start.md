---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1605197188d97f27a884739a72400da2d5818b1a
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371970"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="0e44f-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e44f-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="0e44f-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e44f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="0e44f-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e44f-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="0e44f-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="0e44f-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="0e44f-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="0e44f-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0e44f-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e44f-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="0e44f-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e44f-110">Run the app.</span></span>
> * <span data-ttu-id="0e44f-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="0e44f-111">Examine the project files.</span></span>

<span data-ttu-id="0e44f-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="0e44f-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="0e44f-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0e44f-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0e44f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e44f-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0e44f-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e44f-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0e44f-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e44f-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="0e44f-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="0e44f-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0e44f-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e44f-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e44f-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0e44f-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="0e44f-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="0e44f-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="0e44f-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="0e44f-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="0e44f-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="0e44f-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="0e44f-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="0e44f-126">Selecione **ASP.NET Core 3.0** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="0e44f-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="0e44f-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0e44f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e44f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e44f-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0e44f-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="0e44f-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="0e44f-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="0e44f-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0e44f-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="0e44f-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="0e44f-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="0e44f-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0e44f-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="0e44f-136">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="0e44f-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="0e44f-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="0e44f-137">Select **Yes**.</span></span>

  <span data-ttu-id="0e44f-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0e44f-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0e44f-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e44f-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0e44f-140">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0e44f-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="0e44f-141">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e44f-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="0e44f-142">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="0e44f-142">Open the project</span></span>

<span data-ttu-id="0e44f-143">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0e44f-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="0e44f-144">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e44f-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0e44f-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e44f-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e44f-146">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e44f-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0e44f-147">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e44f-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0e44f-148">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e44f-149">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="0e44f-150">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="0e44f-151">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="0e44f-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0e44f-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e44f-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="0e44f-153">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e44f-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0e44f-154">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="0e44f-155">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e44f-156">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0e44f-157">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0e44f-158">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e44f-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0e44f-159">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e44f-159">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0e44f-160">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-160">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="0e44f-161">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="0e44f-161">Examine the project files</span></span>

<span data-ttu-id="0e44f-162">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="0e44f-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="0e44f-163">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="0e44f-163">Pages folder</span></span>

<span data-ttu-id="0e44f-164">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="0e44f-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="0e44f-165">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="0e44f-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="0e44f-166">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="0e44f-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="0e44f-167">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="0e44f-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="0e44f-168">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="0e44f-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="0e44f-169">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="0e44f-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="0e44f-170">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="0e44f-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="0e44f-171">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-171">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="0e44f-172">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="0e44f-172">wwwroot folder</span></span>

<span data-ttu-id="0e44f-173">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="0e44f-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="0e44f-174">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="0e44f-175">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="0e44f-175">appSettings.json</span></span>

<span data-ttu-id="0e44f-176">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="0e44f-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="0e44f-177">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="0e44f-178">Module.vb</span><span class="sxs-lookup"><span data-stu-id="0e44f-178">Program.cs</span></span>

<span data-ttu-id="0e44f-179">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="0e44f-179">Contains the entry point for the program.</span></span> <span data-ttu-id="0e44f-180">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-180">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="0e44f-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0e44f-181">Startup.cs</span></span>

<span data-ttu-id="0e44f-182">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e44f-182">Contains code that configures app behavior.</span></span> <span data-ttu-id="0e44f-183">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0e44f-184">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0e44f-184">Next steps</span></span>

<span data-ttu-id="0e44f-185">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="0e44f-185">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0e44f-186">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="0e44f-186">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0e44f-187">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="0e44f-187">This is the first tutorial of a series.</span></span> <span data-ttu-id="0e44f-188">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e44f-188">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="0e44f-189">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="0e44f-189">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="0e44f-190">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="0e44f-190">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0e44f-191">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e44f-191">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="0e44f-192">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e44f-192">Run the app.</span></span>
> * <span data-ttu-id="0e44f-193">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="0e44f-193">Examine the project files.</span></span>

<span data-ttu-id="0e44f-194">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="0e44f-194">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="0e44f-196">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0e44f-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0e44f-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e44f-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0e44f-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e44f-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0e44f-199">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e44f-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="0e44f-200">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="0e44f-200">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0e44f-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e44f-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e44f-202">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-202">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="0e44f-203">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-203">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="0e44f-205">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-205">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="0e44f-206">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="0e44f-206">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="0e44f-208">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0e44f-208">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="0e44f-210">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="0e44f-210">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0e44f-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e44f-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e44f-213">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0e44f-213">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="0e44f-214">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="0e44f-214">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="0e44f-215">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0e44f-215">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="0e44f-216">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="0e44f-216">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="0e44f-217">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0e44f-217">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="0e44f-218">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="0e44f-218">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="0e44f-219">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="0e44f-219">Select **Yes**.</span></span>

  <span data-ttu-id="0e44f-220">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0e44f-220">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0e44f-221">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e44f-221">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0e44f-222">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0e44f-222">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="0e44f-223">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0e44f-223">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="0e44f-224">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="0e44f-224">Open the project</span></span>

<span data-ttu-id="0e44f-225">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0e44f-225">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="0e44f-226">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0e44f-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0e44f-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e44f-227">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e44f-228">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e44f-228">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0e44f-229">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0e44f-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0e44f-230">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-230">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e44f-231">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-231">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="0e44f-232">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-232">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="0e44f-233">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="0e44f-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="0e44f-234">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="0e44f-234">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0e44f-235">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="0e44f-235">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="0e44f-237">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="0e44f-237">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0e44f-239">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e44f-239">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="0e44f-240">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e44f-240">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0e44f-241">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-241">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="0e44f-242">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-242">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0e44f-243">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-243">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0e44f-244">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="0e44f-244">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="0e44f-245">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="0e44f-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0e44f-246">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="0e44f-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="0e44f-248">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="0e44f-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0e44f-250">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0e44f-250">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0e44f-251">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="0e44f-251">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0e44f-252">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="0e44f-252">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="0e44f-253">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="0e44f-253">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0e44f-254">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="0e44f-254">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="0e44f-256">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="0e44f-256">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="0e44f-258">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="0e44f-258">Examine the project files</span></span>

<span data-ttu-id="0e44f-259">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="0e44f-259">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="0e44f-260">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="0e44f-260">Pages folder</span></span>

<span data-ttu-id="0e44f-261">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="0e44f-261">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="0e44f-262">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="0e44f-262">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="0e44f-263">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="0e44f-263">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="0e44f-264">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="0e44f-264">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="0e44f-265">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="0e44f-265">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="0e44f-266">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="0e44f-266">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="0e44f-267">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="0e44f-267">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="0e44f-268">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-268">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="0e44f-269">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="0e44f-269">wwwroot folder</span></span>

<span data-ttu-id="0e44f-270">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="0e44f-270">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="0e44f-271">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-271">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="0e44f-272">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="0e44f-272">appSettings.json</span></span>

<span data-ttu-id="0e44f-273">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="0e44f-273">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="0e44f-274">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-274">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="0e44f-275">Module.vb</span><span class="sxs-lookup"><span data-stu-id="0e44f-275">Program.cs</span></span>

<span data-ttu-id="0e44f-276">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="0e44f-276">Contains the entry point for the program.</span></span> <span data-ttu-id="0e44f-277">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-277">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="0e44f-278">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0e44f-278">Startup.cs</span></span>

<span data-ttu-id="0e44f-279">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="0e44f-279">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="0e44f-280">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0e44f-280">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e44f-281">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0e44f-281">Additional resources</span></span>

* [<span data-ttu-id="0e44f-282">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="0e44f-282">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="0e44f-283">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0e44f-283">Next steps</span></span>

<span data-ttu-id="0e44f-284">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="0e44f-284">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0e44f-285">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="0e44f-285">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end