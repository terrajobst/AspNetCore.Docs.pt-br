---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 6/3/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 207f5a5ab5e21d8cd8f6cf2f63641b94d8077ae5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555796"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="d4054-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4054-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="d4054-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d4054-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d4054-106">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="d4054-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="d4054-107">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d4054-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="d4054-108">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="d4054-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="d4054-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="d4054-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d4054-110">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d4054-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="d4054-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d4054-111">Run the app.</span></span>
> * <span data-ttu-id="d4054-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="d4054-112">Examine the project files.</span></span>

<span data-ttu-id="d4054-113">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="d4054-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="d4054-115">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="d4054-115">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d4054-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4054-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d4054-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d4054-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d4054-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d4054-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="d4054-119">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="d4054-119">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d4054-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4054-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d4054-121">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="d4054-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="d4054-122">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="d4054-122">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="d4054-124">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="d4054-124">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="d4054-125">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="d4054-125">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="d4054-127">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d4054-127">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="d4054-129">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="d4054-129">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d4054-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d4054-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d4054-132">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="d4054-132">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="d4054-133">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="d4054-133">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="d4054-134">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d4054-134">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="d4054-135">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="d4054-135">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="d4054-136">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d4054-136">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="d4054-137">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="d4054-137">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="d4054-138">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="d4054-138">Select **Yes**.</span></span>

  <span data-ttu-id="d4054-139">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="d4054-139">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d4054-140">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d4054-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d4054-141">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d4054-141">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="d4054-142">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d4054-142">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="d4054-143">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="d4054-143">Open the project</span></span>

<span data-ttu-id="d4054-144">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="d4054-144">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="d4054-145">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d4054-145">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d4054-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d4054-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d4054-147">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="d4054-147">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="d4054-148">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d4054-148">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d4054-149">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d4054-149">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d4054-150">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="d4054-150">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="d4054-151">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="d4054-151">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="d4054-152">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="d4054-152">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="d4054-153">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="d4054-153">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d4054-154">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="d4054-154">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="d4054-156">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="d4054-156">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d4054-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d4054-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="d4054-159">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="d4054-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="d4054-160">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d4054-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="d4054-161">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="d4054-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d4054-162">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="d4054-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d4054-163">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="d4054-163">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="d4054-164">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="d4054-164">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d4054-165">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="d4054-165">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="d4054-167">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="d4054-167">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d4054-169">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d4054-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d4054-170">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="d4054-170">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="d4054-171">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d4054-171">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="d4054-172">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="d4054-172">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="d4054-173">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="d4054-173">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="d4054-175">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="d4054-175">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="d4054-177">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="d4054-177">Examine the project files</span></span>

<span data-ttu-id="d4054-178">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="d4054-178">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="d4054-179">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="d4054-179">Pages folder</span></span>

<span data-ttu-id="d4054-180">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="d4054-180">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="d4054-181">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="d4054-181">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="d4054-182">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="d4054-182">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="d4054-183">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="d4054-183">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="d4054-184">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="d4054-184">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="d4054-185">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="d4054-185">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="d4054-186">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="d4054-186">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="d4054-187">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d4054-187">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="d4054-188">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="d4054-188">wwwroot folder</span></span>

<span data-ttu-id="d4054-189">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="d4054-189">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="d4054-190">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d4054-190">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="d4054-191">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="d4054-191">appSettings.json</span></span>

<span data-ttu-id="d4054-192">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="d4054-192">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="d4054-193">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d4054-193">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="d4054-194">Module.vb</span><span class="sxs-lookup"><span data-stu-id="d4054-194">Program.cs</span></span>

<span data-ttu-id="d4054-195">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="d4054-195">Contains the entry point for the program.</span></span> <span data-ttu-id="d4054-196">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d4054-196">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="d4054-197">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="d4054-197">Startup.cs</span></span>

<span data-ttu-id="d4054-198">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="d4054-198">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="d4054-199">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d4054-199">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4054-200">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d4054-200">Additional resources</span></span>

* [<span data-ttu-id="d4054-201">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="d4054-201">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="d4054-202">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="d4054-202">Next steps</span></span>

<span data-ttu-id="d4054-203">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="d4054-203">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d4054-204">Criou um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d4054-204">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="d4054-205">Executou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d4054-205">Ran the app.</span></span>
> * <span data-ttu-id="d4054-206">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="d4054-206">Examined the project files.</span></span>

<span data-ttu-id="d4054-207">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="d4054-207">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d4054-208">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="d4054-208">Add a model</span></span>](xref:tutorials/razor-pages/model)
