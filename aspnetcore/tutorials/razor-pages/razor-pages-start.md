---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1d264ca4a605d8291e273a8f054c92e7eefa5548
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468841"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="a3226-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a3226-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="a3226-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a3226-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a3226-106">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="a3226-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="a3226-107">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3226-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a3226-108">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="a3226-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a3226-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="a3226-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a3226-110">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a3226-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="a3226-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3226-111">Run the app.</span></span>
> * <span data-ttu-id="a3226-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="a3226-112">Examine the project files.</span></span>

<span data-ttu-id="a3226-113">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a3226-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="a3226-115">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="a3226-115">Create a Razor Pages web app</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="a3226-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3226-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a3226-117">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a3226-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="a3226-118">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3226-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="a3226-119">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="a3226-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="a3226-120">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="a3226-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="a3226-122">Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="a3226-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="a3226-124">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="a3226-124">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# [<a name="visual-studio-code"></a><span data-ttu-id="a3226-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a3226-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a3226-127">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a3226-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a3226-128">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="a3226-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="a3226-129">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a3226-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="a3226-130">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="a3226-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="a3226-131">O comando `code` abre a pasta *RazorPagesMovie* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a3226-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="a3226-132">Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes de 'RazorPagesMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="a3226-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="a3226-133">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="a3226-133">Select **Yes**</span></span>

# [<a name="visual-studio-for-mac"></a><span data-ttu-id="a3226-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a3226-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a3226-135">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a3226-135">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="a3226-136">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a3226-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="a3226-137">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="a3226-137">Open the project</span></span>

<span data-ttu-id="a3226-138">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="a3226-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a3226-139">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="a3226-139">Run the app</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="a3226-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a3226-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a3226-141">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a3226-141">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a3226-142">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3226-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a3226-143">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a3226-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a3226-144">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="a3226-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="a3226-145">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="a3226-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a3226-146">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="a3226-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="a3226-147">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a3226-147">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a3226-148">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a3226-148">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a3226-150">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a3226-150">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# [<a name="visual-studio-code"></a><span data-ttu-id="a3226-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a3226-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="a3226-153">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a3226-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a3226-154">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a3226-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="a3226-155">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="a3226-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a3226-156">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="a3226-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a3226-157">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="a3226-157">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="a3226-158">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a3226-158">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a3226-159">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a3226-159">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a3226-161">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a3226-161">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)
  
# [<a name="visual-studio-for-mac"></a><span data-ttu-id="a3226-163">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a3226-163">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a3226-164">Pressione **Cmd-Opt-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="a3226-164">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a3226-165">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a3226-165">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="a3226-166">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="a3226-166">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a3226-167">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="a3226-167">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="a3226-169">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="a3226-169">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="a3226-171">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="a3226-171">Examine the project files</span></span>

<span data-ttu-id="a3226-172">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="a3226-172">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a3226-173">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="a3226-173">Pages folder</span></span>

<span data-ttu-id="a3226-174">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="a3226-174">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="a3226-175">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="a3226-175">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="a3226-176">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="a3226-176">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="a3226-177">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="a3226-177">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a3226-178">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="a3226-178">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a3226-179">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="a3226-179">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a3226-180">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="a3226-180">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a3226-181">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="a3226-181">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a3226-182">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="a3226-182">wwwroot folder</span></span>

<span data-ttu-id="a3226-183">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="a3226-183">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a3226-184">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="a3226-184">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a3226-185">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="a3226-185">appSettings.json</span></span>

<span data-ttu-id="a3226-186">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="a3226-186">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a3226-187">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="a3226-187">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a3226-188">Module.vb</span><span class="sxs-lookup"><span data-stu-id="a3226-188">Program.cs</span></span>

<span data-ttu-id="a3226-189">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="a3226-189">Contains the entry point for the program.</span></span> <span data-ttu-id="a3226-190">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="a3226-190">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a3226-191">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a3226-191">Startup.cs</span></span>

<span data-ttu-id="a3226-192">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="a3226-192">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="a3226-193">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="a3226-193">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a3226-194">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a3226-194">Additional resources</span></span>

* [<span data-ttu-id="a3226-195">Versão deste tutorial no YouTube</span><span class="sxs-lookup"><span data-stu-id="a3226-195">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="a3226-196">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a3226-196">Next steps</span></span>

<span data-ttu-id="a3226-197">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="a3226-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a3226-198">Criou um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a3226-198">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="a3226-199">Executou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a3226-199">Ran the app.</span></span>
> * <span data-ttu-id="a3226-200">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="a3226-200">Examined the project files.</span></span>

<span data-ttu-id="a3226-201">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="a3226-201">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a3226-202">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="a3226-202">Add a model</span></span>](xref:tutorials/razor-pages/model)
