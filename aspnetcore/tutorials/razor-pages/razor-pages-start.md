---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 29d9369cfa6a4c76f015b5a819a27dfa280d4075
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346405"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="fddb1-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fddb1-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="fddb1-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fddb1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fddb1-106">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="fddb1-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="fddb1-107">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fddb1-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="fddb1-108">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="fddb1-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="fddb1-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="fddb1-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fddb1-110">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fddb1-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="fddb1-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddb1-111">Run the app.</span></span>
> * <span data-ttu-id="fddb1-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="fddb1-112">Examine the project files.</span></span>

<span data-ttu-id="fddb1-113">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="fddb1-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="fddb1-115">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="fddb1-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fddb1-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fddb1-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fddb1-117">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="fddb1-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="fddb1-118">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fddb1-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="fddb1-119">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="fddb1-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="fddb1-120">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="fddb1-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="fddb1-122">Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="fddb1-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="fddb1-124">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="fddb1-124">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fddb1-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fddb1-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fddb1-127">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fddb1-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="fddb1-128">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="fddb1-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="fddb1-129">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="fddb1-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="fddb1-130">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="fddb1-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="fddb1-131">O comando `code` abre a pasta *RazorPagesMovie* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fddb1-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="fddb1-132">Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes de 'RazorPagesMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="fddb1-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="fddb1-133">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="fddb1-133">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="fddb1-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fddb1-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fddb1-135">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="fddb1-135">From a terminal, run the following commands:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
```

<span data-ttu-id="fddb1-136">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fddb1-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="fddb1-137">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="fddb1-137">Open the project</span></span>

<span data-ttu-id="fddb1-138">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="fddb1-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="fddb1-139">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="fddb1-139">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fddb1-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fddb1-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fddb1-141">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="fddb1-141">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="fddb1-142">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddb1-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="fddb1-143">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fddb1-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fddb1-144">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="fddb1-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="fddb1-145">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="fddb1-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="fddb1-146">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="fddb1-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fddb1-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fddb1-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fddb1-148">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="fddb1-148">Press **Ctrl-F5** to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="fddb1-149">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fddb1-149">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="fddb1-150">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="fddb1-150">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="fddb1-151">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="fddb1-151">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="fddb1-152">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="fddb1-152">Localhost only serves web requests from the local computer.</span></span>
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="fddb1-153">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fddb1-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fddb1-154">Selecione **Executar > Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddb1-154">Select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="fddb1-155">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fddb1-155">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<!-- End of VS tabs -->

---

* <span data-ttu-id="fddb1-156">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="fddb1-156">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="fddb1-157">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="fddb1-157">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="fddb1-159">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="fddb1-159">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="examine-the-project-files"></a><span data-ttu-id="fddb1-161">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="fddb1-161">Examine the project files</span></span>

<span data-ttu-id="fddb1-162">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="fddb1-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="fddb1-163">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="fddb1-163">Pages folder</span></span>

<span data-ttu-id="fddb1-164">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="fddb1-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="fddb1-165">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="fddb1-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="fddb1-166">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="fddb1-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="fddb1-167">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="fddb1-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="fddb1-168">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="fddb1-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="fddb1-169">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="fddb1-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="fddb1-170">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="fddb1-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="fddb1-171">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="fddb1-171">For more information, see <xref:mvc/views/layout>.</span></span>


### <a name="wwwroot-folder"></a><span data-ttu-id="fddb1-172">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="fddb1-172">wwwroot folder</span></span>

<span data-ttu-id="fddb1-173">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="fddb1-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="fddb1-174">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fddb1-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="fddb1-175">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="fddb1-175">appSettings.json</span></span>

<span data-ttu-id="fddb1-176">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="fddb1-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="fddb1-177">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fddb1-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="fddb1-178">Module.vb</span><span class="sxs-lookup"><span data-stu-id="fddb1-178">Program.cs</span></span>

<span data-ttu-id="fddb1-179">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="fddb1-179">Contains the entry point for the program.</span></span> <span data-ttu-id="fddb1-180">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="fddb1-180">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="fddb1-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="fddb1-181">Startup.cs</span></span>

<span data-ttu-id="fddb1-182">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="fddb1-182">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="fddb1-183">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="fddb1-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fddb1-184">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fddb1-184">Additional resources</span></span>

* [<span data-ttu-id="fddb1-185">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="fddb1-185">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="fddb1-186">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="fddb1-186">Next steps</span></span>

<span data-ttu-id="fddb1-187">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="fddb1-187">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fddb1-188">Criou um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fddb1-188">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="fddb1-189">Executou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fddb1-189">Ran the app.</span></span>
> * <span data-ttu-id="fddb1-190">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="fddb1-190">Examined the project files.</span></span>

<span data-ttu-id="fddb1-191">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="fddb1-191">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="fddb1-192">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="fddb1-192">Add a model</span></span>](xref:tutorials/razor-pages/model)
