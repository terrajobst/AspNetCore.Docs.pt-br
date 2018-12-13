---
title: Introdução às Páginas do Razor no ASP.NET Core
author: rick-anderson
monikerRange: '>= aspnetcore-2.2'
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1152ebfcee48a46ecd28c941fce32d3fc1e05c41
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861622"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="c81c1-104">Tutorial: introdução ao Razor Pages no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c81c1-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="c81c1-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c81c1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c81c1-106">Este tutorial ensina as noções básicas de criação de um aplicativo Web de Páginas do Razor do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c81c1-106">This tutorial teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="c81c1-107">O aplicativo gerencia um banco de dados de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="c81c1-107">The app manages a database of movie titles.</span></span> <span data-ttu-id="c81c1-108">Você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="c81c1-108">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c81c1-109">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c81c1-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="c81c1-110">Adicionar e gerar o scaffolding de um modelo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-110">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c81c1-111">Trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c81c1-111">Work with a database.</span></span>
> * <span data-ttu-id="c81c1-112">Adicionar pesquisa e validação.</span><span class="sxs-lookup"><span data-stu-id="c81c1-112">Add search and validation.</span></span>

<span data-ttu-id="c81c1-113">No final, você terá um aplicativo que pode gerenciar e exibir itens de títulos de filmes.</span><span class="sxs-lookup"><span data-stu-id="c81c1-113">At the end, you have an app that can manage and display movie titles items.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c81c1-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="c81c1-114">Prerequisites</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="c81c1-115">Criar um aplicativo Web do Razor</span><span class="sxs-lookup"><span data-stu-id="c81c1-115">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c81c1-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c81c1-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c81c1-117">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="c81c1-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="c81c1-118">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c81c1-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="c81c1-119">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="c81c1-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="c81c1-120">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar/colar código.</span><span class="sxs-lookup"><span data-stu-id="c81c1-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy/paste code.</span></span>
 <span data-ttu-id="c81c1-121">![novo aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="c81c1-121">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>

* <span data-ttu-id="c81c1-122">Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="c81c1-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="c81c1-124">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="c81c1-124">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

* <span data-ttu-id="c81c1-126">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="c81c1-126">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c81c1-127">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-127">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c81c1-128">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c81c1-128">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c81c1-129">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="c81c1-129">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="c81c1-130">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="c81c1-130">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="c81c1-131">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="c81c1-131">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c81c1-132">Na imagem anterior, o número da porta é 5001.</span><span class="sxs-lookup"><span data-stu-id="c81c1-132">In the preceding image, the port number is 5001.</span></span> <span data-ttu-id="c81c1-133">Quando você executar o aplicativo, verá um número de porta diferente.</span><span class="sxs-lookup"><span data-stu-id="c81c1-133">When you run the app, you'll see a different port number.</span></span>

  <span data-ttu-id="c81c1-134">Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="c81c1-134">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c81c1-135">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="c81c1-135">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c81c1-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c81c1-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c81c1-137">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="c81c1-137">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c81c1-138">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="c81c1-138">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c81c1-139">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c81c1-139">Run the following command:</span></span>

   ```console
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

  * <span data-ttu-id="c81c1-140">Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes de 'RazorPagesMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="c81c1-140">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>  <span data-ttu-id="c81c1-141">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="c81c1-141">Select **Yes**</span></span>

  * <span data-ttu-id="c81c1-142">`dotnet new webapp -o RazorPagesMovie`: cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="c81c1-142">`dotnet new webapp -o RazorPagesMovie`: creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="c81c1-143">`code -r RazorPagesMovie`: carrega o arquivo de projeto *RazorPagesMovie.csproj* no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c81c1-143">`code -r RazorPagesMovie`: Loads the *RazorPagesMovie.csproj* project file in Visual Studio Code.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="c81c1-144">Iniciar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c81c1-144">Launch the app</span></span>

* <span data-ttu-id="c81c1-145">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="c81c1-145">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="c81c1-146">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c81c1-146">Visual Studio Code starts starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="c81c1-147">A barra de endereços mostra `localhost:port:5001` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="c81c1-147">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c81c1-148">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="c81c1-148">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c81c1-149">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="c81c1-149">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c81c1-150">Iniciar o aplicativo com **Ctrl+F5** (modo de não depuração) permite que você faça alterações de código, salve o arquivo, atualize o navegador e veja as alterações de código.</span><span class="sxs-lookup"><span data-stu-id="c81c1-150">Launching the app with **Ctrl+F5** (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c81c1-151">Muitos desenvolvedores preferem usar o modo sem depuração para atualizar a página e exibir alterações.</span><span class="sxs-lookup"><span data-stu-id="c81c1-151">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c81c1-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c81c1-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c81c1-153">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c81c1-153">From a terminal, run the following commands:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

<span data-ttu-id="c81c1-154">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar e executar um projeto de Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="c81c1-154">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create and run a Razor Pages project.</span></span> <span data-ttu-id="c81c1-155">Abra um navegador em http://localhost:5000 para exibir o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-155">Open a browser to http://localhost:5000 to view the application.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="c81c1-156">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="c81c1-156">Open the project</span></span>

<span data-ttu-id="c81c1-157">Pressione Ctrl + C para encerrar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-157">Press Ctrl+C to shut down the application.</span></span>

<span data-ttu-id="c81c1-158">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="c81c1-158">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

### <a name="launch-the-app"></a><span data-ttu-id="c81c1-159">Iniciar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c81c1-159">Launch the app</span></span>

<span data-ttu-id="c81c1-160">Selecione **Executar > Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-160">Select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c81c1-161">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="c81c1-161">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

* <span data-ttu-id="c81c1-162">Selecione **Aceitar** para dar consentimento de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="c81c1-162">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c81c1-163">Este aplicativo não acompanha informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="c81c1-163">This app doesn't track personal information.</span></span> <span data-ttu-id="c81c1-164">O código de modelo gerado inclui ativos para ajudar a cumprir o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="c81c1-164">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="c81c1-166">A imagem a seguir mostra o aplicativo depois de aceitar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="c81c1-166">The following image shows the app after accepting tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="project-files-and-folders"></a><span data-ttu-id="c81c1-168">Pastas e arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="c81c1-168">Project files and folders</span></span>

<span data-ttu-id="c81c1-169">A tabela a seguir lista os arquivos e pastas no projeto.</span><span class="sxs-lookup"><span data-stu-id="c81c1-169">The following table lists the files and folders in the project.</span></span> <span data-ttu-id="c81c1-170">Neste ponto do tutorial, o arquivo *Startup.cs* é o mais importante a ser compreendido.</span><span class="sxs-lookup"><span data-stu-id="c81c1-170">At this point in the tutorial, the *Startup.cs* file is the most important to understand.</span></span> <span data-ttu-id="c81c1-171">Não é necessário examinar cada link fornecido abaixo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-171">You don't need to review each link provided below.</span></span> <span data-ttu-id="c81c1-172">Os links são fornecidos como uma referência para quando você precisar de mais informações sobre um arquivo ou pasta no projeto.</span><span class="sxs-lookup"><span data-stu-id="c81c1-172">The links are provided as a reference when you need more information on a file or folder in the project.</span></span>

| <span data-ttu-id="c81c1-173">Arquivo ou pasta</span><span class="sxs-lookup"><span data-stu-id="c81c1-173">File or folder</span></span>              | <span data-ttu-id="c81c1-174">Finalidade</span><span class="sxs-lookup"><span data-stu-id="c81c1-174">Purpose</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="c81c1-175">*wwwroot*</span><span class="sxs-lookup"><span data-stu-id="c81c1-175">*wwwroot*</span></span> | <span data-ttu-id="c81c1-176">Contém arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c81c1-176">Contains static files.</span></span> <span data-ttu-id="c81c1-177">Confira [Arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="c81c1-177">See [Static files](xref:fundamentals/static-files).</span></span> |
| <span data-ttu-id="c81c1-178">*Páginas*</span><span class="sxs-lookup"><span data-stu-id="c81c1-178">*Pages*</span></span> | <span data-ttu-id="c81c1-179">Pasta para [Páginas do Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="c81c1-179">Folder for [Razor Pages](xref:razor-pages/index).</span></span> |
| <span data-ttu-id="c81c1-180">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="c81c1-180">*appsettings.json*</span></span> | [<span data-ttu-id="c81c1-181">Configuração</span><span class="sxs-lookup"><span data-stu-id="c81c1-181">Configuration</span></span>](xref:fundamentals/configuration/index) |
| <span data-ttu-id="c81c1-182">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="c81c1-182">*Program.cs*</span></span> | <span data-ttu-id="c81c1-183">[Hospeda](xref:fundamentals/host/index) o aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c81c1-183">[Hosts](xref:fundamentals/host/index) the ASP.NET Core app.</span></span>|
| <span data-ttu-id="c81c1-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="c81c1-184">*Startup.cs*</span></span> | <span data-ttu-id="c81c1-185">Configura os serviços e o pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="c81c1-185">Configures services and the request pipeline.</span></span> <span data-ttu-id="c81c1-186">Consulte [Startup (Inicialização)](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c81c1-186">See [Startup](xref:fundamentals/startup).</span></span>|

### <a name="the-pages-folder"></a><span data-ttu-id="c81c1-187">A pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="c81c1-187">The Pages folder</span></span>

<span data-ttu-id="c81c1-188">O arquivo *_Layout.cshtml* contém elementos HTML comuns (scripts e folhas de estilo) e define o layout para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-188">The *_Layout.cshtml* file contains common HTML elements (scripts and stylesheets) and sets the layout for the application.</span></span> <span data-ttu-id="c81c1-189">Por exemplo, quando você clica em **RazorPagesMovie**, **Home** ou **Privacy**, você vê os mesmos elementos.</span><span class="sxs-lookup"><span data-stu-id="c81c1-189">For example, when you click on **RazorPagesMovie**, **Home**, or **Privacy**, you see the same elements.</span></span> <span data-ttu-id="c81c1-190">Os elementos comuns incluem o menu de navegação na parte superior e o cabeçalho na parte inferior da janela.</span><span class="sxs-lookup"><span data-stu-id="c81c1-190">The common elements include the navigation menu on the top and the header on the bottom of the window.</span></span> <span data-ttu-id="c81c1-191">Veja [Layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="c81c1-191">See [Layout](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="c81c1-192">O arquivo *_ViewImports.cshtml* contém diretivas do Razor que são importadas para cada Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="c81c1-192">The *_ViewImports.cshtml* file contains Razor directives that are imported into each Razor Page.</span></span> <span data-ttu-id="c81c1-193">Veja [Importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="c81c1-193">See [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives) for more information.</span></span>

<span data-ttu-id="c81c1-194">O *_ViewStart.cshtml* define a propriedade `Layout` das Páginas do Razor para usar o arquivo *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c81c1-194">The *_ViewStart.cshtml* sets the Razor Pages `Layout` property to use the *_Layout.cshtml* file.</span></span> <span data-ttu-id="c81c1-195">Veja [Layout](xref:mvc/views/layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="c81c1-195">See [Layout](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="c81c1-196">O arquivo *_ValidationScriptsPartial.cshtml* fornece uma referência a scripts de validação [jQuery](https://jquery.com/).</span><span class="sxs-lookup"><span data-stu-id="c81c1-196">The *_ValidationScriptsPartial.cshtml* file provides a reference to [jQuery](https://jquery.com/) validation scripts.</span></span> <span data-ttu-id="c81c1-197">Quando as páginas `Create` e `Edit` forem adicionadas mais adiante no tutorial, o arquivo *_ValidationScriptsPartial.cshtml* será usado.</span><span class="sxs-lookup"><span data-stu-id="c81c1-197">When the `Create` and `Edit` pages are added later in the tutorial, the *_ValidationScriptsPartial.cshtml* file will be used.</span></span>

<span data-ttu-id="c81c1-198">As páginas `Index`, `Error` e `Privacy` são fornecidas para:</span><span class="sxs-lookup"><span data-stu-id="c81c1-198">`Index`, `Error`, and `Privacy` pages are provided to:</span></span>

* <span data-ttu-id="c81c1-199">`Index`: Iniciar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c81c1-199">`Index`: Start an app.</span></span>
* <span data-ttu-id="c81c1-200">`Error`: Exibir informações de erro.</span><span class="sxs-lookup"><span data-stu-id="c81c1-200">`Error`: Display error information.</span></span>
* <span data-ttu-id="c81c1-201">`Privacy`: Especificar detalhes sobre a política de privacidade do site.</span><span class="sxs-lookup"><span data-stu-id="c81c1-201">`Privacy`: Specify details about the site's privacy policy.</span></span>

<span data-ttu-id="c81c1-202">Para este tutorial, as páginas anteriores não são usadas.</span><span class="sxs-lookup"><span data-stu-id="c81c1-202">For this tutorial, the preceding pages are not used.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c81c1-203">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c81c1-203">Visual Studio</span></span>](#tab/visual-studio)

<a name="f7"></a>
### <a name="use-f7-to-toggle-between-a-razor-page-and-the-pagemodel"></a><span data-ttu-id="c81c1-204">Use F7 para alternar entre um Razor Page e o PageModel</span><span class="sxs-lookup"><span data-stu-id="c81c1-204">Use F7 to toggle between a Razor Page and the PageModel</span></span>

<span data-ttu-id="c81c1-205">Alternância com F7 entre um Razor Page (arquivo *\*.cshtml*) e o arquivo C# (*\*.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="c81c1-205">F7 toggles between a Razor Page (*\*.cshtml* file) and the C# file (*\*.cshtml.cs*).</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c81c1-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c81c1-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- TODO review  Need something in these tabs -->

<span data-ttu-id="c81c1-207">Por convenção, o Razor Page (arquivo *\*.cshtml*) e o `PageModel` associado têm o mesmo nome de arquivo raiz.</span><span class="sxs-lookup"><span data-stu-id="c81c1-207">By convention, the Razor Page (*\*.cshtml* file) and the associated `PageModel` have the same root file name.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c81c1-208">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c81c1-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c81c1-209">Por convenção, o Razor Page (arquivo *\*.cshtml*) e o `PageModel` associado têm o mesmo nome de arquivo raiz.</span><span class="sxs-lookup"><span data-stu-id="c81c1-209">By convention, the Razor Page (*\*.cshtml* file) and the associated `PageModel` have the same root file name.</span></span>

---

> [!div class="step-by-step"]
> [<span data-ttu-id="c81c1-210">Próximo: adicionando um modelo</span><span class="sxs-lookup"><span data-stu-id="c81c1-210">Next: Adding a model</span></span>](xref:tutorials/razor-pages/model)