---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/28/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 77c7d4a318610fcd424da0485abd41d11e3fad6a
ms.sourcegitcommit: fbc66827e319d28bebed678ea5fd42f582fe3c34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493558"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="d042f-103">Criar interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d042f-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="d042f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d042f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d042f-105">Exibições do Razor, páginas, controladores, modelos de página, [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="d042f-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="d042f-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="d042f-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="d042f-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="d042f-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="d042f-108">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="d042f-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="d042f-109">Este recurso requer [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="d042f-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="d042f-110">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d042f-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="d042f-111">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="d042f-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d042f-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d042f-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d042f-113">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="d042f-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d042f-114">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d042f-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="d042f-115">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="d042f-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="d042f-116">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="d042f-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="d042f-117">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="d042f-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="d042f-118">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="d042f-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="d042f-119">Um RCL tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d042f-119">An RCL has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d042f-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d042f-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d042f-121">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="d042f-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="d042f-122">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d042f-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="d042f-123">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="d042f-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="d042f-124">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="d042f-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="d042f-125">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="d042f-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="d042f-126">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="d042f-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="d042f-127">Consulte [layout de páginas RCL](#afs) para criar um RCL que expõe o `~/Pages` conteúdo em `~/Areas/Pages`em vez de.</span><span class="sxs-lookup"><span data-stu-id="d042f-127">See [RCL Pages layout](#afs) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-rcl-content"></a><span data-ttu-id="d042f-128">Fazendo referência ao conteúdo do RCL</span><span class="sxs-lookup"><span data-stu-id="d042f-128">Referencing RCL content</span></span>

<span data-ttu-id="d042f-129">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="d042f-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="d042f-130">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="d042f-130">NuGet package.</span></span> <span data-ttu-id="d042f-131">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="d042f-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="d042f-132">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="d042f-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="d042f-133">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="d042f-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="d042f-134">Passo a passo: Criar um projeto RCL e usar de um projeto Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d042f-134">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="d042f-135">Você pode baixar o [projeto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="d042f-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="d042f-136">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="d042f-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="d042f-137">Você pode deixar comentários [nesse problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="d042f-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="d042f-138">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="d042f-138">Test the download app</span></span>

<span data-ttu-id="d042f-139">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="d042f-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d042f-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d042f-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d042f-141">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d042f-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="d042f-142">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d042f-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d042f-143">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d042f-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d042f-144">Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="d042f-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="d042f-145">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d042f-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="d042f-146">Siga as instruções em [Testar WebApp1](#test)</span><span class="sxs-lookup"><span data-stu-id="d042f-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="d042f-147">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="d042f-147">Create an RCL</span></span>

<span data-ttu-id="d042f-148">Nesta seção, um RCL é criado.</span><span class="sxs-lookup"><span data-stu-id="d042f-148">In this section, an RCL is created.</span></span> <span data-ttu-id="d042f-149">Arquivos Razor são adicionados à RCL.</span><span class="sxs-lookup"><span data-stu-id="d042f-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d042f-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d042f-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d042f-151">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="d042f-151">Create the RCL project:</span></span>

* <span data-ttu-id="d042f-152">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="d042f-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d042f-153">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d042f-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="d042f-154">Nomeie o aplicativo **RazorUIClassLib** > **Okey**.</span><span class="sxs-lookup"><span data-stu-id="d042f-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="d042f-155">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="d042f-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="d042f-156">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="d042f-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="d042f-157">Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d042f-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d042f-158">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d042f-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d042f-159">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="d042f-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="d042f-160">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="d042f-160">The preceding commands:</span></span>

* <span data-ttu-id="d042f-161">Cria o `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="d042f-161">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="d042f-162">Criam uma página Razor _Message e a adicionam à RCL.</span><span class="sxs-lookup"><span data-stu-id="d042f-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="d042f-163">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="d042f-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="d042f-164">Cria uma [viewstart](xref:mvc/views/layout#running-code-before-each-view) de arquivo e o adiciona à RCL.</span><span class="sxs-lookup"><span data-stu-id="d042f-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="d042f-165">O *viewstart* arquivo é necessário para usar o layout do projeto páginas Razor (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="d042f-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="d042f-166">Adicionar pastas e arquivos Razor ao projeto</span><span class="sxs-lookup"><span data-stu-id="d042f-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="d042f-167">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d042f-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="d042f-168">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="d042f-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="d042f-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="d042f-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="d042f-170">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d042f-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="d042f-171">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="d042f-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="d042f-172">Para obter mais informações sobre *viewimports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="d042f-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="d042f-173">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="d042f-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="d042f-174">A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="d042f-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="d042f-175">*RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="d042f-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="d042f-176">Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="d042f-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d042f-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d042f-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d042f-178">Crie aplicativo Web Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="d042f-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="d042f-179">No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="d042f-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="d042f-180">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d042f-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="d042f-181">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="d042f-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="d042f-182">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="d042f-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="d042f-183">Selecione **Aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="d042f-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="d042f-184">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="d042f-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="d042f-185">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.</span><span class="sxs-lookup"><span data-stu-id="d042f-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="d042f-186">Marque **RazorUIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="d042f-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="d042f-187">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.</span><span class="sxs-lookup"><span data-stu-id="d042f-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="d042f-188">Na caixa de diálogo **Gerenciador de Referências**, marque **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="d042f-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="d042f-189">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d042f-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d042f-190">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d042f-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d042f-191">Crie um aplicativo Web Razor Pages e um arquivo de solução que contenha o aplicativo Razor Pages e o RCL:</span><span class="sxs-lookup"><span data-stu-id="d042f-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="d042f-192">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="d042f-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="d042f-193">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="d042f-193">Test WebApp1</span></span>

<span data-ttu-id="d042f-194">Verifique se a biblioteca de classes da interface do usuário do Razor está em uso:</span><span class="sxs-lookup"><span data-stu-id="d042f-194">Verify the Razor UI class library is in use:</span></span>

* <span data-ttu-id="d042f-195">Navegue para `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="d042f-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="d042f-196">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="d042f-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="d042f-197">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="d042f-197">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="d042f-198">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="d042f-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="d042f-199">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="d042f-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="d042f-200">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d042f-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="d042f-201">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="d042f-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="d042f-202">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="d042f-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="d042f-203">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="d042f-203">RCL Pages layout</span></span>

<span data-ttu-id="d042f-204">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="d042f-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="d042f-205">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="d042f-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="d042f-206">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="d042f-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="d042f-207">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d042f-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="d042f-208">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="d042f-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="d042f-209">Criar um RCL com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="d042f-209">Create an RCL with static assets</span></span>

<span data-ttu-id="d042f-210">Um RCL pode exigir ativos estáticos complementares que podem ser referenciados pelo aplicativo de consumo do RCL.</span><span class="sxs-lookup"><span data-stu-id="d042f-210">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="d042f-211">ASP.NET Core permite criar RCLs que incluem ativos estáticos disponíveis para um aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="d042f-211">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="d042f-212">Para incluir ativos complementares como parte de um RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua todos os arquivos necessários nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="d042f-212">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="d042f-213">Ao empacotar um RCL, todos os ativos complementares na pasta *wwwroot* são incluídos automaticamente no pacote e disponibilizados para aplicativos que fazem referência ao pacote.</span><span class="sxs-lookup"><span data-stu-id="d042f-213">When packing an RCL, all companion assets in the *wwwroot* folder are included in the package automatically and are made available to apps referencing the package.</span></span>

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="d042f-214">Consumir conteúdo de um RCL referenciado</span><span class="sxs-lookup"><span data-stu-id="d042f-214">Consume content from a referenced RCL</span></span>

<span data-ttu-id="d042f-215">Os arquivos incluídos na pasta *wwwroot* do RCL são expostos ao aplicativo de consumo sob o prefixo `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="d042f-215">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="d042f-216">Por exemplo, uma biblioteca chamada *Razor. class. lib* resulta em um caminho para o conteúdo estático `_content/Razor.Class.Lib/`em.</span><span class="sxs-lookup"><span data-stu-id="d042f-216">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="d042f-217">O aplicativo de consumo faz referência a ativos estáticos fornecidos `<script>`pela `<style>`biblioteca `<img>`com,, e outras marcas HTML.</span><span class="sxs-lookup"><span data-stu-id="d042f-217">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="d042f-218">O aplicativo de consumo deve ter o [suporte de arquivo estático](xref:fundamentals/static-files) habilitado.</span><span class="sxs-lookup"><span data-stu-id="d042f-218">The consuming app must have [static file support](xref:fundamentals/static-files) enabled.</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="d042f-219">Fluxo de desenvolvimento de vários projetos</span><span class="sxs-lookup"><span data-stu-id="d042f-219">Multi-project development flow</span></span>

<span data-ttu-id="d042f-220">Quando o aplicativo de consumo for executado:</span><span class="sxs-lookup"><span data-stu-id="d042f-220">When the consuming app runs:</span></span>

* <span data-ttu-id="d042f-221">Os ativos no RCL permanecem em suas pastas originais.</span><span class="sxs-lookup"><span data-stu-id="d042f-221">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="d042f-222">Os ativos não são movidos para o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="d042f-222">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="d042f-223">Qualquer alteração na pasta *wwwroot* do RCL é refletida no aplicativo de consumo depois que o RCL é recriado e sem recriar o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="d042f-223">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="d042f-224">Quando o RCL é criado, é produzido um manifesto que descreve os locais estáticos de ativos da Web.</span><span class="sxs-lookup"><span data-stu-id="d042f-224">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="d042f-225">O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados.</span><span class="sxs-lookup"><span data-stu-id="d042f-225">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="d042f-226">Quando um novo ativo é adicionado a um RCL, o RCL deve ser recriado para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.</span><span class="sxs-lookup"><span data-stu-id="d042f-226">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="d042f-227">Publicar</span><span class="sxs-lookup"><span data-stu-id="d042f-227">Publish</span></span>

<span data-ttu-id="d042f-228">Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes referenciados são copiados para a pasta *wwwroot* do `_content/{LIBRARY NAME}/`aplicativo publicado em.</span><span class="sxs-lookup"><span data-stu-id="d042f-228">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end
