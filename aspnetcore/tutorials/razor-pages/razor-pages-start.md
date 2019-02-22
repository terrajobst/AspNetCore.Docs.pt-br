---
title: 'Tutorial: Introdução às Páginas do Razor no ASP.NET Core'
author: rick-anderson
description: Esta série de tutoriais mostra como usar Razor Pages no ASP.NET Core. Saiba como criar um modelo, gerar código para Razor Pages, usar o Entity Framework Core e o SQL Server para acesso a dados, adicionar funcionalidade de pesquisa, adicionar validação de entrada e usar migrações para atualizar o modelo.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: acebe52719e1876dc6808441fcff6fe849e36983
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410408"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="3cfb8-104">Tutorial: Introdução às Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3cfb8-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="3cfb8-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3cfb8-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3cfb8-106">Este é o primeiro tutorial de uma série.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="3cfb8-107">[A série](xref:tutorials/razor-pages/index) ensina as noções básicas de criação de um aplicativo Web do Razor Pages do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="3cfb8-108">No final da série, você terá um aplicativo que gerencia um banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="3cfb8-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3cfb8-110">Criar um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="3cfb8-111">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-111">Run the app.</span></span>
> * <span data-ttu-id="3cfb8-112">Examinar os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-112">Examine the project files.</span></span>

<span data-ttu-id="3cfb8-113">No final deste tutorial, você terá um aplicativo Web em funcionamento do Razor Pages que você criará em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="3cfb8-115">Criar um aplicativo Web das Páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="3cfb8-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3cfb8-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cfb8-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3cfb8-117">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="3cfb8-118">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="3cfb8-119">Nomeie o projeto **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="3cfb8-120">É importante nomear o projeto *RazorPagesMovie* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="3cfb8-122">Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![novo Aplicativo Web ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="3cfb8-124">O seguinte projeto inicial é criado:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-124">The following starter project is created:</span></span>

  ![Gerenciador de Soluções](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3cfb8-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cfb8-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3cfb8-127">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3cfb8-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3cfb8-128">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="3cfb8-129">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="3cfb8-130">O comando `dotnet new` cria um projeto do Razor Pages na pasta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="3cfb8-131">O comando `code` abre a pasta *RazorPagesMovie* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="3cfb8-132">Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes de 'RazorPagesMovie'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="3cfb8-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="3cfb8-133">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="3cfb8-133">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3cfb8-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3cfb8-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3cfb8-135">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-135">From a terminal, run the following commands:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
```

<span data-ttu-id="3cfb8-136">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="3cfb8-137">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="3cfb8-137">Open the project</span></span>

<span data-ttu-id="3cfb8-138">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *RazorPagesMovie.csproj*.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-web-app"></a><span data-ttu-id="3cfb8-139">Executar o aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="3cfb8-139">Run the web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3cfb8-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cfb8-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3cfb8-141">Pressione Ctrl + F5 para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-141">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="3cfb8-142">O Visual Studio inicia o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) e executa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="3cfb8-143">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3cfb8-144">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="3cfb8-145">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="3cfb8-146">Quando o Visual Studio cria um projeto Web, uma porta aleatória é usada para o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="3cfb8-147">Na imagem anterior, o número da porta é 5001.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-147">In the preceding image, the port number is 5001.</span></span> <span data-ttu-id="3cfb8-148">Quando você executar o aplicativo, verá um número de porta diferente.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-148">When you run the app, you'll see a different port number.</span></span>
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3cfb8-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cfb8-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3cfb8-150">Pressione **Ctrl-F5** para execução sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-150">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="3cfb8-151">O Visual Studio Code inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicializa um navegador e navega até `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-151">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="3cfb8-152">A barra de endereços mostra `localhost:port#` e não algo como `example.com`.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-152">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="3cfb8-153">Isso ocorre porque `localhost` é o nome do host padrão do computador local.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-153">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="3cfb8-154">Localhost serve somente solicitações da Web do computador local.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-154">Localhost only serves web requests from the local computer.</span></span>
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3cfb8-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3cfb8-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3cfb8-156">Selecione **Executar > Iniciar Sem Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-156">Select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="3cfb8-157">O Visual Studio inicia o [Kestrel](xref:fundamentals/servers/kestrel), inicia um navegador e navega para `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-157">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

* <span data-ttu-id="3cfb8-158">Na página inicial do aplicativo, selecione **Aceitar** para dar consentimento de acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-158">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="3cfb8-159">Este aplicativo não rastreia informações pessoais, mas o modelo de projeto inclui o recurso de consentimento no caso de você precisar estar em conformidade com o [RGPD (Regulamento Geral sobre a Proteção de Dados)](xref:security/gdpr) da União Europeia.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-159">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="3cfb8-161">A imagem a seguir mostra o aplicativo depois de consentir o acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-161">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicial ou de Índice](razor-pages-start/_static/home2.2.png)

## <a name="examine-the-project-files"></a><span data-ttu-id="3cfb8-163">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="3cfb8-163">Examine the project files</span></span>

<span data-ttu-id="3cfb8-164">Aqui está uma visão geral das pastas do projeto principal e os arquivos que você usará para trabalhar em tutoriais posteriores.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-164">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="3cfb8-165">Pasta Páginas</span><span class="sxs-lookup"><span data-stu-id="3cfb8-165">Pages folder</span></span>

<span data-ttu-id="3cfb8-166">Contém as Razor Pages e os arquivos de suporte.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-166">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="3cfb8-167">Cada Razor Page é um par de arquivos:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-167">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="3cfb8-168">Um arquivo *.cshtml* que contém a marcação HTML com o código C# usando a sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-168">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="3cfb8-169">Um arquivo *.cshtml.cs* que contém o código C# que manipula os eventos de página.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-169">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="3cfb8-170">Arquivos de suporte têm nomes que começam com um sublinhado.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-170">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="3cfb8-171">Por exemplo, o arquivo *_Layout.cshtml* configura os elementos de interface do usuário comuns a todas as páginas.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-171">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="3cfb8-172">Esse arquivo define o menu de navegação na parte superior da página e a notificação de direitos autorais na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-172">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="3cfb8-173">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-173">For more information, see <xref:mvc/views/layout>.</span></span>


### <a name="wwwroot-folder"></a><span data-ttu-id="3cfb8-174">Pasta wwwroot</span><span class="sxs-lookup"><span data-stu-id="3cfb8-174">wwwroot folder</span></span>

<span data-ttu-id="3cfb8-175">Contém os arquivos estáticos, como arquivos HTML, arquivos JavaScript e arquivos CSS.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-175">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="3cfb8-176">Para obter mais informações, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-176">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3cfb8-177">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="3cfb8-177">appSettings.json</span></span>

<span data-ttu-id="3cfb8-178">Contém dados de configuração, como cadeias de conexão.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-178">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="3cfb8-179">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-179">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="3cfb8-180">Module.vb</span><span class="sxs-lookup"><span data-stu-id="3cfb8-180">Program.cs</span></span>

<span data-ttu-id="3cfb8-181">Contém o ponto de entrada para o programa.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-181">Contains the entry point for the program.</span></span> <span data-ttu-id="3cfb8-182">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-182">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="3cfb8-183">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3cfb8-183">Startup.cs</span></span>

<span data-ttu-id="3cfb8-184">Contém o código que configura o comportamento do aplicativo, como se ele requer o consentimento para cookies.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-184">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="3cfb8-185">Para obter mais informações, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-185">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3cfb8-186">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3cfb8-186">Next steps</span></span>

<span data-ttu-id="3cfb8-187">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-187">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3cfb8-188">Criou um aplicativo Web do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-188">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="3cfb8-189">Executou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-189">Ran the app.</span></span>
> * <span data-ttu-id="3cfb8-190">Examinou os arquivos de projeto.</span><span class="sxs-lookup"><span data-stu-id="3cfb8-190">Examined the project files.</span></span>

<span data-ttu-id="3cfb8-191">Vá para o próximo tutorial da série:</span><span class="sxs-lookup"><span data-stu-id="3cfb8-191">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3cfb8-192">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="3cfb8-192">Add a model</span></span>](xref:tutorials/razor-pages/model)
