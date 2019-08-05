---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 57a10895c718c539ece280afcb27cb4033c7fb45
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682800"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="3bd90-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bd90-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="3bd90-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3bd90-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="3bd90-106">Este é o primeiro tutorial de uma série que ensina as noções básicas da criação de um aplicativo Web do Razor Pages no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bd90-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="3bd90-107">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="3bd90-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="3bd90-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="3bd90-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3bd90-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3bd90-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3bd90-110">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd90-110">Run the app.</span></span>
> * <span data-ttu-id="3bd90-111">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd90-111">Examine the project files.</span></span>

<span data-ttu-id="3bd90-112">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="3bd90-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="3bd90-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="3bd90-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bd90-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bd90-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bd90-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bd90-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bd90-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3bd90-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="3bd90-118">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="3bd90-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bd90-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bd90-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3bd90-120">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3bd90-121">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="3bd90-122">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="3bd90-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="3bd90-123">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="3bd90-124">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="3bd90-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="3bd90-125">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="3bd90-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="3bd90-126">Selecione **ASP.NET Core 3.0** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="3bd90-128">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="3bd90-128">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bd90-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bd90-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3bd90-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3bd90-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3bd90-132">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd90-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="3bd90-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3bd90-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="3bd90-134">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="3bd90-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="3bd90-135">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3bd90-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="3bd90-136">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="3bd90-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="3bd90-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="3bd90-137">Select **Yes**.</span></span>

  <span data-ttu-id="3bd90-138">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd90-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bd90-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3bd90-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3bd90-140">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bd90-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="3bd90-141">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3bd90-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="3bd90-142">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="3bd90-142">Open the project</span></span>

<span data-ttu-id="3bd90-143">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="3bd90-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="3bd90-144">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3bd90-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bd90-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bd90-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3bd90-146">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3bd90-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="3bd90-147">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd90-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3bd90-148">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3bd90-149">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="3bd90-150">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="3bd90-151">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="3bd90-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bd90-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bd90-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="3bd90-153">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3bd90-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3bd90-154">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="3bd90-155">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3bd90-156">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3bd90-157">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bd90-158">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3bd90-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3bd90-159">Pressione **Alt-Cmd-Enter** para executar sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3bd90-159">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="3bd90-160">Como alternativa, navegue até a barra de menus e acesse Executar > Iniciar sem depuração.</span><span class="sxs-lookup"><span data-stu-id="3bd90-160">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="3bd90-161">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-161">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="3bd90-162">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="3bd90-162">Examine the project files</span></span>

<span data-ttu-id="3bd90-163">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="3bd90-163">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3bd90-164">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="3bd90-164">Pages folder</span></span>

<span data-ttu-id="3bd90-165">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="3bd90-165">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3bd90-166">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="3bd90-166">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3bd90-167">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="3bd90-167">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3bd90-168">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="3bd90-168">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="3bd90-169">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="3bd90-169">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3bd90-170">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="3bd90-170">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3bd90-171">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="3bd90-171">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3bd90-172">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-172">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="3bd90-173">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="3bd90-173">wwwroot folder</span></span>

<span data-ttu-id="3bd90-174">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="3bd90-174">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3bd90-175">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-175">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3bd90-176">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="3bd90-176">appSettings.json</span></span>

<span data-ttu-id="3bd90-177">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="3bd90-177">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="3bd90-178">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-178">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3bd90-179">Module.vb</span><span class="sxs-lookup"><span data-stu-id="3bd90-179">Program.cs</span></span>

<span data-ttu-id="3bd90-180">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="3bd90-180">Contains the entry point for the program.</span></span> <span data-ttu-id="3bd90-181">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-181">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3bd90-182">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3bd90-182">Startup.cs</span></span>

<span data-ttu-id="3bd90-183">contém código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd90-183">Contains code that configures app behavior.</span></span> <span data-ttu-id="3bd90-184">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-184">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3bd90-185">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3bd90-185">Next steps</span></span>

<span data-ttu-id="3bd90-186">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="3bd90-186">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3bd90-187">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="3bd90-187">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3bd90-188">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="3bd90-188">This is the first tutorial of a series.</span></span> <span data-ttu-id="3bd90-189">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bd90-189">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="3bd90-190">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="3bd90-190">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="3bd90-191">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="3bd90-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3bd90-192">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3bd90-192">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3bd90-193">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd90-193">Run the app.</span></span>
> * <span data-ttu-id="3bd90-194">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd90-194">Examine the project files.</span></span>

<span data-ttu-id="3bd90-195">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="3bd90-195">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="3bd90-197">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="3bd90-197">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bd90-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bd90-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bd90-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bd90-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bd90-200">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3bd90-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="3bd90-201">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="3bd90-201">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bd90-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bd90-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3bd90-203">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-203">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="3bd90-204">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-204">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="3bd90-206">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-206">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="3bd90-207">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="3bd90-207">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="3bd90-209">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3bd90-209">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="3bd90-211">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="3bd90-211">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bd90-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bd90-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3bd90-214">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3bd90-214">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3bd90-215">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd90-215">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="3bd90-216">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3bd90-216">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="3bd90-217">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="3bd90-217">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="3bd90-218">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3bd90-218">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="3bd90-219">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="3bd90-219">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="3bd90-220">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="3bd90-220">Select **Yes**.</span></span>

  <span data-ttu-id="3bd90-221">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3bd90-221">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bd90-222">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3bd90-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3bd90-223">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bd90-223">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="3bd90-224">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3bd90-224">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="3bd90-225">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="3bd90-225">Open the project</span></span>

<span data-ttu-id="3bd90-226">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="3bd90-226">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="3bd90-227">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3bd90-227">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3bd90-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3bd90-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3bd90-229">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3bd90-229">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="3bd90-230">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bd90-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3bd90-231">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-231">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3bd90-232">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-232">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="3bd90-233">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-233">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="3bd90-234">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="3bd90-234">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="3bd90-235">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="3bd90-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3bd90-236">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="3bd90-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="3bd90-238">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="3bd90-238">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3bd90-240">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3bd90-240">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="3bd90-241">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3bd90-241">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3bd90-242">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-242">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="3bd90-243">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-243">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3bd90-244">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-244">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3bd90-245">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bd90-245">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="3bd90-246">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="3bd90-246">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3bd90-247">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="3bd90-247">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="3bd90-249">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="3bd90-249">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3bd90-251">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3bd90-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="3bd90-252">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3bd90-252">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3bd90-253">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3bd90-253">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="3bd90-254">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="3bd90-254">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3bd90-255">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="3bd90-255">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="3bd90-257">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="3bd90-257">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="3bd90-259">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="3bd90-259">Examine the project files</span></span>

<span data-ttu-id="3bd90-260">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="3bd90-260">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3bd90-261">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="3bd90-261">Pages folder</span></span>

<span data-ttu-id="3bd90-262">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="3bd90-262">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3bd90-263">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="3bd90-263">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3bd90-264">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="3bd90-264">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3bd90-265">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="3bd90-265">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="3bd90-266">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="3bd90-266">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3bd90-267">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="3bd90-267">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3bd90-268">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="3bd90-268">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3bd90-269">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-269">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="3bd90-270">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="3bd90-270">wwwroot folder</span></span>

<span data-ttu-id="3bd90-271">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="3bd90-271">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3bd90-272">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-272">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3bd90-273">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="3bd90-273">appSettings.json</span></span>

<span data-ttu-id="3bd90-274">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="3bd90-274">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="3bd90-275">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-275">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3bd90-276">Module.vb</span><span class="sxs-lookup"><span data-stu-id="3bd90-276">Program.cs</span></span>

<span data-ttu-id="3bd90-277">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="3bd90-277">Contains the entry point for the program.</span></span> <span data-ttu-id="3bd90-278">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-278">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3bd90-279">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3bd90-279">Startup.cs</span></span>

<span data-ttu-id="3bd90-280">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="3bd90-280">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="3bd90-281">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3bd90-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3bd90-282">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3bd90-282">Additional resources</span></span>

* [<span data-ttu-id="3bd90-283">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="3bd90-283">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="3bd90-284">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3bd90-284">Next steps</span></span>

<span data-ttu-id="3bd90-285">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="3bd90-285">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3bd90-286">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="3bd90-286">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
