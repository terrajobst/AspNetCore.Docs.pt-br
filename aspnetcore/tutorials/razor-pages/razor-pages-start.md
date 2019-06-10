---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 6/3/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: d843e47ccb5180fab34b4c4c4a4b5cbda21289bf
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491214"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="4ced3-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ced3-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="4ced3-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4ced3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4ced3-106">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="4ced3-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="4ced3-107">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4ced3-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="4ced3-108">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="4ced3-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="4ced3-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="4ced3-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ced3-110">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4ced3-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="4ced3-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4ced3-111">Run the app.</span></span>
> * <span data-ttu-id="4ced3-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="4ced3-112">Examine the project files.</span></span>

<span data-ttu-id="4ced3-113">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4ced3-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="4ced3-115">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="4ced3-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ced3-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ced3-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ced3-117">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4ced3-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="4ced3-118">Crie um aplicativo Web ASP.NET Core e selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="4ced3-118">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="4ced3-120">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="4ced3-120">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="4ced3-121">É importante nomear o projeto como *RazorPagesMovie* para que os namespaces sejam correspondidos quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="4ced3-121">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="4ced3-123">Selecione **ASP.NET Core 2.2** na lista suspensa **Aplicativo Web** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="4ced3-123">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="4ced3-125">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="4ced3-125">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ced3-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ced3-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4ced3-128">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4ced3-128">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4ced3-129">Altere para o diretório (`cd`) que contém o projeto.</span><span class="sxs-lookup"><span data-stu-id="4ced3-129">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="4ced3-130">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="4ced3-130">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="4ced3-131">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="4ced3-131">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="4ced3-132">O comando `code` abre a pasta *RazorPagesMovie* na instância atual do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="4ced3-132">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="4ced3-133">Depois que o ícone de chama do OmniSharp da barra de status ficar verde, uma caixa de diálogo perguntará se **Os ativos necessários para criar e depurar estão ausentes no "RazorPagesMovie". Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="4ced3-133">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="4ced3-134">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="4ced3-134">Select **Yes**.</span></span>

  <span data-ttu-id="4ced3-135">Um diretório *.vscode*, contendo os arquivos *launch.json* e *tasks.json*, é adicionado ao diretório raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="4ced3-135">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ced3-136">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ced3-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4ced3-137">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="4ced3-137">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="4ced3-138">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4ced3-138">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="4ced3-139">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="4ced3-139">Open the project</span></span>

<span data-ttu-id="4ced3-140">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="4ced3-140">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="4ced3-141">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="4ced3-141">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4ced3-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ced3-142">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ced3-143">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="4ced3-143">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="4ced3-144">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4ced3-144">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="4ced3-145">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4ced3-145">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4ced3-146">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="4ced3-146">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="4ced3-147">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="4ced3-147">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="4ced3-148">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="4ced3-148">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="4ced3-149">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="4ced3-149">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4ced3-150">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="4ced3-150">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4ced3-152">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="4ced3-152">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4ced3-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ced3-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="4ced3-155">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="4ced3-155">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4ced3-156">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4ced3-156">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="4ced3-157">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="4ced3-157">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="4ced3-158">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="4ced3-158">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="4ced3-159">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="4ced3-159">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="4ced3-160">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="4ced3-160">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4ced3-161">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="4ced3-161">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="4ced3-163">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="4ced3-163">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4ced3-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ced3-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="4ced3-166">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="4ced3-166">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="4ced3-167">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="4ced3-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="4ced3-168">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="4ced3-168">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="4ced3-169">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="4ced3-169">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="4ced3-171">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="4ced3-171">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="4ced3-173">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="4ced3-173">Examine the project files</span></span>

<span data-ttu-id="4ced3-174">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="4ced3-174">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="4ced3-175">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="4ced3-175">Pages folder</span></span>

<span data-ttu-id="4ced3-176">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="4ced3-176">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="4ced3-177">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="4ced3-177">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="4ced3-178">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="4ced3-178">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="4ced3-179">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="4ced3-179">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="4ced3-180">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="4ced3-180">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="4ced3-181">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="4ced3-181">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="4ced3-182">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="4ced3-182">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="4ced3-183">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="4ced3-183">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="4ced3-184">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="4ced3-184">wwwroot folder</span></span>

<span data-ttu-id="4ced3-185">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="4ced3-185">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="4ced3-186">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="4ced3-186">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="4ced3-187">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="4ced3-187">appSettings.json</span></span>

<span data-ttu-id="4ced3-188">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="4ced3-188">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="4ced3-189">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4ced3-189">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="4ced3-190">Module.vb</span><span class="sxs-lookup"><span data-stu-id="4ced3-190">Program.cs</span></span>

<span data-ttu-id="4ced3-191">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="4ced3-191">Contains the entry point for the program.</span></span> <span data-ttu-id="4ced3-192">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="4ced3-192">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="4ced3-193">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="4ced3-193">Startup.cs</span></span>

<span data-ttu-id="4ced3-194">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="4ced3-194">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="4ced3-195">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4ced3-195">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ced3-196">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4ced3-196">Additional resources</span></span>

* [<span data-ttu-id="4ced3-197">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="4ced3-197">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="4ced3-198">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="4ced3-198">Next steps</span></span>

<span data-ttu-id="4ced3-199">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="4ced3-199">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ced3-200">Criou um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="4ced3-200">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="4ced3-201">Executou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4ced3-201">Ran the app.</span></span>
> * <span data-ttu-id="4ced3-202">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="4ced3-202">Examined the project files.</span></span>

<span data-ttu-id="4ced3-203">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="4ced3-203">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="4ced3-204">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="4ced3-204">Add a model</span></span>](xref:tutorials/razor-pages/model)
