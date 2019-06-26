---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/24/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 96ef8fc055a6b92cd0808d02031d917b8446f305
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394755"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="72844-103">Criar a interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72844-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="72844-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="72844-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="72844-105">Exibições do Razor, páginas, controladores, modelos de página [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components), e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="72844-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="72844-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="72844-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="72844-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="72844-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="72844-108">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="72844-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="72844-109">Este recurso requer [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="72844-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="72844-110">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72844-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="72844-111">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="72844-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72844-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72844-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="72844-113">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="72844-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="72844-114">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="72844-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="72844-115">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="72844-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="72844-116">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="72844-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="72844-117">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="72844-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="72844-118">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="72844-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="72844-119">Uma RCL tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="72844-119">An RCL has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="72844-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="72844-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="72844-121">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="72844-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="72844-122">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="72844-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="72844-123">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="72844-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="72844-124">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="72844-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="72844-125">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="72844-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="72844-126">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="72844-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="72844-127">Ver [layout de páginas RCL](#afs) para criar uma RCL que expõe conteúdo em `~/Pages` em vez de `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="72844-127">See [RCL Pages layout](#afs) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-rcl-content"></a><span data-ttu-id="72844-128">Fazendo referência a conteúdo da RCL</span><span class="sxs-lookup"><span data-stu-id="72844-128">Referencing RCL content</span></span>

<span data-ttu-id="72844-129">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="72844-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="72844-130">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="72844-130">NuGet package.</span></span> <span data-ttu-id="72844-131">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="72844-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="72844-132">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="72844-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="72844-133">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="72844-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="72844-134">Passo a passo: Criar um projeto da RCL e usar a partir de um projeto de páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="72844-134">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="72844-135">Você pode baixar o [projeto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="72844-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="72844-136">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="72844-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="72844-137">Você pode deixar comentários [nesse problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="72844-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="72844-138">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="72844-138">Test the download app</span></span>

<span data-ttu-id="72844-139">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="72844-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72844-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72844-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="72844-141">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="72844-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="72844-142">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="72844-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="72844-143">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="72844-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="72844-144">Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="72844-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="72844-145">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="72844-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="72844-146">Siga as instruções em [Testar WebApp1](#test)</span><span class="sxs-lookup"><span data-stu-id="72844-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="72844-147">Criar uma RCL</span><span class="sxs-lookup"><span data-stu-id="72844-147">Create an RCL</span></span>

<span data-ttu-id="72844-148">Nesta seção, uma RCL é criado.</span><span class="sxs-lookup"><span data-stu-id="72844-148">In this section, an RCL is created.</span></span> <span data-ttu-id="72844-149">Arquivos Razor são adicionados à RCL.</span><span class="sxs-lookup"><span data-stu-id="72844-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72844-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72844-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="72844-151">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="72844-151">Create the RCL project:</span></span>

* <span data-ttu-id="72844-152">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="72844-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="72844-153">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="72844-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="72844-154">Nomeie o aplicativo **RazorUIClassLib** > **Okey**.</span><span class="sxs-lookup"><span data-stu-id="72844-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="72844-155">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="72844-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="72844-156">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="72844-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="72844-157">Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="72844-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="72844-158">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="72844-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="72844-159">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="72844-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="72844-160">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="72844-160">The preceding commands:</span></span>

* <span data-ttu-id="72844-161">Cria o `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="72844-161">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="72844-162">Criam uma página Razor _Message e a adicionam à RCL.</span><span class="sxs-lookup"><span data-stu-id="72844-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="72844-163">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="72844-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="72844-164">Cria uma [viewstart](xref:mvc/views/layout#running-code-before-each-view) de arquivo e o adiciona à RCL.</span><span class="sxs-lookup"><span data-stu-id="72844-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="72844-165">O *viewstart* arquivo é necessário para usar o layout do projeto páginas Razor (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="72844-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="72844-166">Adicionar pastas e arquivos Razor ao projeto</span><span class="sxs-lookup"><span data-stu-id="72844-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="72844-167">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72844-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="72844-168">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="72844-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="72844-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="72844-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="72844-170">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="72844-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="72844-171">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="72844-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="72844-172">Para obter mais informações sobre *viewimports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="72844-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="72844-173">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="72844-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="72844-174">A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="72844-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="72844-175">*RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="72844-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="72844-176">Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="72844-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="72844-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="72844-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="72844-178">Crie aplicativo Web Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="72844-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="72844-179">No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="72844-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="72844-180">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="72844-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="72844-181">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="72844-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="72844-182">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="72844-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="72844-183">Selecione **Aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="72844-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="72844-184">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="72844-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="72844-185">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.</span><span class="sxs-lookup"><span data-stu-id="72844-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="72844-186">Marque **RazorUIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="72844-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="72844-187">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.</span><span class="sxs-lookup"><span data-stu-id="72844-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="72844-188">Na caixa de diálogo **Gerenciador de Referências**, marque **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="72844-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="72844-189">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="72844-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="72844-190">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="72844-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="72844-191">Crie um aplicativo web páginas Razor e um arquivo de solução que contém o aplicativo páginas Razor e na RCL:</span><span class="sxs-lookup"><span data-stu-id="72844-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="72844-192">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="72844-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="72844-193">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="72844-193">Test WebApp1</span></span>

<span data-ttu-id="72844-194">Verifique se que a biblioteca de classes Razor da interface do usuário está em uso:</span><span class="sxs-lookup"><span data-stu-id="72844-194">Verify the Razor UI class library is in use:</span></span>

* <span data-ttu-id="72844-195">Navegue para `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="72844-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="72844-196">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="72844-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="72844-197">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="72844-197">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="72844-198">Por exemplo, adicione *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ao WebApp1, e a Page1 ao WebApp1 terá precedência sobre a Page1 na RCL.</span><span class="sxs-lookup"><span data-stu-id="72844-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="72844-199">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="72844-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="72844-200">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="72844-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="72844-201">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="72844-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="72844-202">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="72844-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="72844-203">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="72844-203">RCL Pages layout</span></span>

<span data-ttu-id="72844-204">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="72844-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="72844-205">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="72844-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="72844-206">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="72844-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="72844-207">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="72844-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="72844-208">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="72844-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="72844-209">Criar uma RCL com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="72844-209">Create an RCL with static assets</span></span>

<span data-ttu-id="72844-210">Uma RCL pode exigir ativos estáticos de complementar que podem ser referenciados pelo aplicativo de consumo da RCL.</span><span class="sxs-lookup"><span data-stu-id="72844-210">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="72844-211">ASP.NET Core permite a criação de RCLs que incluem ativos estáticos que estão disponíveis para um aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="72844-211">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="72844-212">Para incluir ativos complementar como parte de uma RCL, crie uma *wwwroot* pasta na biblioteca de classes e incluir quaisquer arquivos necessários nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="72844-212">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="72844-213">Ao empacotar uma RCL, todos os ativos de complementar a *wwwroot* pasta são incluídos automaticamente no pacote e são disponibilizados para referenciar o pacote de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="72844-213">When packing an RCL, all companion assets in the *wwwroot* folder are included in the package automatically and are made available to apps referencing the package.</span></span>

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="72844-214">Consumir conteúdo de uma RCL referenciado</span><span class="sxs-lookup"><span data-stu-id="72844-214">Consume content from a referenced RCL</span></span>

<span data-ttu-id="72844-215">Os arquivos incluídos na *wwwroot* pasta da RCL são expostos para o aplicativo de consumo sob o prefixo `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="72844-215">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="72844-216">`{LIBRARY NAME}` o nome do projeto de biblioteca que é convertido em letras minúsculas com períodos (`.`) removido.</span><span class="sxs-lookup"><span data-stu-id="72844-216">`{LIBRARY NAME}` is the library project name converted to lowercase with periods (`.`) removed.</span></span> <span data-ttu-id="72844-217">Por exemplo, uma biblioteca denominada *Razor.Class.Lib* resulta em um caminho para o conteúdo estático em `_content/razorclasslib/`.</span><span class="sxs-lookup"><span data-stu-id="72844-217">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/razorclasslib/`.</span></span>

<span data-ttu-id="72844-218">O aplicativo de consumo referencia ativos estáticos fornecidos pela biblioteca com `<script>`, `<style>`, `<img>`e outras marcas HTML.</span><span class="sxs-lookup"><span data-stu-id="72844-218">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="72844-219">O aplicativo de consumo deve ter [suporte de arquivo estático](xref:fundamentals/static-files) habilitado.</span><span class="sxs-lookup"><span data-stu-id="72844-219">The consuming app must have [static file support](xref:fundamentals/static-files) enabled.</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="72844-220">Fluxo de vários projetos de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="72844-220">Multi-project development flow</span></span>

<span data-ttu-id="72844-221">Quando o aplicativo de consumo é executado:</span><span class="sxs-lookup"><span data-stu-id="72844-221">When the consuming app runs:</span></span>

* <span data-ttu-id="72844-222">Os ativos na RCL mantenha-se em suas pastas originais.</span><span class="sxs-lookup"><span data-stu-id="72844-222">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="72844-223">Os ativos não são movidos para o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="72844-223">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="72844-224">Qualquer alteração dentro a RCL *wwwroot* pasta é refletida no aplicativo de consumo depois que a RCL é reconstruída e sem recompilar o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="72844-224">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="72844-225">Quando a RCL é compilada, um manifesto é produzido que descreve os locais de ativos estáticos web.</span><span class="sxs-lookup"><span data-stu-id="72844-225">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="72844-226">O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de pacotes e projetos referenciados.</span><span class="sxs-lookup"><span data-stu-id="72844-226">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="72844-227">Quando um novo ativo for adicionado a uma RCL, a RCL deve ser recriada para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.</span><span class="sxs-lookup"><span data-stu-id="72844-227">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="72844-228">Publicar</span><span class="sxs-lookup"><span data-stu-id="72844-228">Publish</span></span>

<span data-ttu-id="72844-229">Quando o aplicativo é publicado, os ativos de todos os projetos e pacotes complementares são copiados para o *wwwroot* pasta do aplicativo publicado em `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="72844-229">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>
