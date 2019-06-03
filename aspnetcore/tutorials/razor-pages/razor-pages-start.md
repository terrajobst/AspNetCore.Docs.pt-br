---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 05/30/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: e9f11f68aa138ab74a0ffbbd0e32067bc984606d
ms.sourcegitcommit: 9ae1fd11f39b0a72b2ae42f0b450345e6e306bc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66415667"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="a266c-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a266c-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a266c-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a266c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a266c-106">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="a266c-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="a266c-107">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a266c-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a266c-108">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="a266c-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a266c-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="a266c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a266c-110">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a266c-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a266c-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a266c-111">Run the app.</span></span>
> * <span data-ttu-id="a266c-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="a266c-112">Examine the project files.</span></span>

<span data-ttu-id="a266c-113">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a266c-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="a266c-115">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="a266c-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a266c-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a266c-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a266c-117">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a266c-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="a266c-118">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a266c-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="a266c-119">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a266c-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a266c-120">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="a266c-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="a266c-122">Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="a266c-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="a266c-124">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="a266c-124">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a266c-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a266c-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a266c-127">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a266c-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a266c-128">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="a266c-128">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a266c-129">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a266c-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a266c-130">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="a266c-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a266c-131">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a266c-131">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a266c-132">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="a266c-132">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="a266c-133">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="a266c-133">Select **Yes**.</span></span>

  <span data-ttu-id="a266c-134">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a266c-134">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a266c-135">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a266c-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a266c-136">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a266c-136">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="a266c-137">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a266c-137">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="a266c-138">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="a266c-138">Open the project</span></span>

<span data-ttu-id="a266c-139">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="a266c-139">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a266c-140">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a266c-140">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a266c-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a266c-141">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a266c-142">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a266c-142">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a266c-143">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a266c-143">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a266c-144">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a266c-144">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a266c-145">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="a266c-145">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="a266c-146">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="a266c-146">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a266c-147">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="a266c-147">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="a266c-148">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a266c-148">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a266c-149">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a266c-149">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a266c-151">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a266c-151">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a266c-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a266c-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="a266c-154">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a266c-154">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a266c-155">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a266c-155">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="a266c-156">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a266c-156">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a266c-157">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="a266c-157">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a266c-158">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="a266c-158">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="a266c-159">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a266c-159">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a266c-160">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a266c-160">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a266c-162">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a266c-162">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a266c-164">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a266c-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a266c-165">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a266c-165">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a266c-166">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a266c-166">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="a266c-167">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a266c-167">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a266c-168">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a266c-168">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="a266c-170">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a266c-170">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="a266c-172">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="a266c-172">Examine the project files</span></span>

<span data-ttu-id="a266c-173">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a266c-173">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a266c-174">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="a266c-174">Pages folder</span></span>

<span data-ttu-id="a266c-175">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="a266c-175">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a266c-176">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="a266c-176">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a266c-177">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="a266c-177">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a266c-178">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="a266c-178">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a266c-179">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="a266c-179">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a266c-180">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="a266c-180">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a266c-181">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="a266c-181">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a266c-182">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a266c-182">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a266c-183">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="a266c-183">wwwroot folder</span></span>

<span data-ttu-id="a266c-184">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="a266c-184">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a266c-185">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a266c-185">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a266c-186">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="a266c-186">appSettings.json</span></span>

<span data-ttu-id="a266c-187">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="a266c-187">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a266c-188">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a266c-188">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a266c-189">Module.vb</span><span class="sxs-lookup"><span data-stu-id="a266c-189">Program.cs</span></span>

<span data-ttu-id="a266c-190">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="a266c-190">Contains the entry point for the program.</span></span> <span data-ttu-id="a266c-191">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="a266c-191">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a266c-192">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a266c-192">Startup.cs</span></span>

<span data-ttu-id="a266c-193">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="a266c-193">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="a266c-194">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a266c-194">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a266c-195">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a266c-195">Additional resources</span></span>

* [<span data-ttu-id="a266c-196">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="a266c-196">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="a266c-197">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a266c-197">Next steps</span></span>

<span data-ttu-id="a266c-198">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="a266c-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a266c-199">Criou um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a266c-199">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="a266c-200">Executou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a266c-200">Ran the app.</span></span>
> * <span data-ttu-id="a266c-201">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="a266c-201">Examined the project files.</span></span>

<span data-ttu-id="a266c-202">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="a266c-202">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a266c-203">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="a266c-203">Add a model</span></span>](xref:tutorials/razor-pages/model)
