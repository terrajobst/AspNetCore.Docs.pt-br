---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/22/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 92c04c1ac4c70c6245accf272753bc914aaab860
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081867"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="30c11-103">Criar interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="30c11-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="30c11-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="30c11-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="30c11-105">Exibições do Razor, páginas, controladores, modelos de página, [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="30c11-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="30c11-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="30c11-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="30c11-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="30c11-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="30c11-108">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="30c11-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="30c11-109">Este recurso requer [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="30c11-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="30c11-110">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="30c11-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="30c11-111">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="30c11-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="30c11-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30c11-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="30c11-113">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="30c11-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="30c11-114">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="30c11-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="30c11-115">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="30c11-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="30c11-116">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="30c11-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="30c11-117">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="30c11-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="30c11-118">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="30c11-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="30c11-119">Um RCL tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="30c11-119">An RCL has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="30c11-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30c11-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30c11-121">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="30c11-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="30c11-122">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="30c11-122">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="30c11-123">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="30c11-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="30c11-124">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="30c11-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="30c11-125">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="30c11-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="30c11-126">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="30c11-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="30c11-127">Consulte [layout de páginas RCL](#afs) para criar um RCL que expõe o `~/Pages` conteúdo em `~/Areas/Pages`em vez de.</span><span class="sxs-lookup"><span data-stu-id="30c11-127">See [RCL Pages layout](#afs) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-rcl-content"></a><span data-ttu-id="30c11-128">Fazendo referência ao conteúdo do RCL</span><span class="sxs-lookup"><span data-stu-id="30c11-128">Referencing RCL content</span></span>

<span data-ttu-id="30c11-129">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="30c11-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="30c11-130">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="30c11-130">NuGet package.</span></span> <span data-ttu-id="30c11-131">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="30c11-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="30c11-132">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="30c11-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="30c11-133">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="30c11-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="30c11-134">Passo a passo: Criar um projeto RCL e usar de um projeto Razor Pages</span><span class="sxs-lookup"><span data-stu-id="30c11-134">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="30c11-135">Você pode baixar o [projeto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="30c11-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="30c11-136">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="30c11-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="30c11-137">Você pode deixar comentários [nesse problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="30c11-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="30c11-138">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="30c11-138">Test the download app</span></span>

<span data-ttu-id="30c11-139">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="30c11-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="30c11-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30c11-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30c11-141">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="30c11-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="30c11-142">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30c11-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="30c11-143">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30c11-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30c11-144">Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="30c11-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="30c11-145">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="30c11-145">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="30c11-146">Siga as instruções em [Testar WebApp1](#test)</span><span class="sxs-lookup"><span data-stu-id="30c11-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="30c11-147">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="30c11-147">Create an RCL</span></span>

<span data-ttu-id="30c11-148">Nesta seção, um RCL é criado.</span><span class="sxs-lookup"><span data-stu-id="30c11-148">In this section, an RCL is created.</span></span> <span data-ttu-id="30c11-149">Arquivos Razor são adicionados à RCL.</span><span class="sxs-lookup"><span data-stu-id="30c11-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="30c11-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30c11-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30c11-151">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="30c11-151">Create the RCL project:</span></span>

* <span data-ttu-id="30c11-152">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="30c11-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="30c11-153">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="30c11-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="30c11-154">Nomeie o aplicativo **RazorUIClassLib** > **Okey**.</span><span class="sxs-lookup"><span data-stu-id="30c11-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="30c11-155">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="30c11-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="30c11-156">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="30c11-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="30c11-157">Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="30c11-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="30c11-158">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30c11-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30c11-159">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="30c11-159">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="30c11-160">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="30c11-160">The preceding commands:</span></span>

* <span data-ttu-id="30c11-161">Cria o `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="30c11-161">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="30c11-162">Criam uma página Razor _Message e a adicionam à RCL.</span><span class="sxs-lookup"><span data-stu-id="30c11-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="30c11-163">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="30c11-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="30c11-164">Cria uma [viewstart](xref:mvc/views/layout#running-code-before-each-view) de arquivo e o adiciona à RCL.</span><span class="sxs-lookup"><span data-stu-id="30c11-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="30c11-165">O *viewstart* arquivo é necessário para usar o layout do projeto páginas Razor (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="30c11-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="30c11-166">Adicionar pastas e arquivos Razor ao projeto</span><span class="sxs-lookup"><span data-stu-id="30c11-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="30c11-167">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="30c11-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="30c11-168">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="30c11-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="30c11-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="30c11-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="30c11-170">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="30c11-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="30c11-171">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="30c11-171">For example:</span></span>

```dotnetcli
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="30c11-172">Para obter mais informações sobre *viewimports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="30c11-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="30c11-173">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="30c11-173">Build the class library to verify there are no compiler errors:</span></span>

```dotnetcli
dotnet build RazorUIClassLib
```

<span data-ttu-id="30c11-174">A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="30c11-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="30c11-175">*RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="30c11-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="30c11-176">Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="30c11-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="30c11-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="30c11-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="30c11-178">Crie aplicativo Web Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="30c11-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="30c11-179">No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="30c11-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="30c11-180">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="30c11-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="30c11-181">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="30c11-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="30c11-182">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="30c11-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="30c11-183">Selecione **Aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="30c11-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="30c11-184">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="30c11-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="30c11-185">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.</span><span class="sxs-lookup"><span data-stu-id="30c11-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="30c11-186">Marque **RazorUIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="30c11-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="30c11-187">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.</span><span class="sxs-lookup"><span data-stu-id="30c11-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="30c11-188">Na caixa de diálogo **Gerenciador de Referências**, marque **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="30c11-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="30c11-189">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="30c11-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="30c11-190">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="30c11-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="30c11-191">Crie um aplicativo Web Razor Pages e um arquivo de solução que contenha o aplicativo Razor Pages e o RCL:</span><span class="sxs-lookup"><span data-stu-id="30c11-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="30c11-192">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="30c11-192">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="30c11-193">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="30c11-193">Test WebApp1</span></span>

<span data-ttu-id="30c11-194">Verifique se a biblioteca de classes da interface do usuário do Razor está em uso:</span><span class="sxs-lookup"><span data-stu-id="30c11-194">Verify the Razor UI class library is in use:</span></span>

* <span data-ttu-id="30c11-195">Navegue para `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="30c11-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="30c11-196">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="30c11-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="30c11-197">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="30c11-197">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="30c11-198">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="30c11-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="30c11-199">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="30c11-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="30c11-200">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="30c11-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="30c11-201">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="30c11-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="30c11-202">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="30c11-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="30c11-203">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="30c11-203">RCL Pages layout</span></span>

<span data-ttu-id="30c11-204">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="30c11-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="30c11-205">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="30c11-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="30c11-206">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="30c11-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="30c11-207">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="30c11-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="30c11-208">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="30c11-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="30c11-209">Criar um RCL com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="30c11-209">Create an RCL with static assets</span></span>

<span data-ttu-id="30c11-210">Um RCL pode exigir ativos estáticos complementares que podem ser referenciados pelo aplicativo de consumo do RCL.</span><span class="sxs-lookup"><span data-stu-id="30c11-210">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="30c11-211">ASP.NET Core permite criar RCLs que incluem ativos estáticos disponíveis para um aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="30c11-211">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="30c11-212">Para incluir ativos complementares como parte de um RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua todos os arquivos necessários nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="30c11-212">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="30c11-213">Ao empacotar um RCL, todos os ativos complementares na pasta *wwwroot* são incluídos automaticamente no pacote.</span><span class="sxs-lookup"><span data-stu-id="30c11-213">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="30c11-214">Excluir ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="30c11-214">Exclude static assets</span></span>

<span data-ttu-id="30c11-215">Para excluir ativos estáticos, adicione o caminho de exclusão desejado `$(DefaultItemExcludes)` ao grupo de propriedades no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="30c11-215">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="30c11-216">Separe as entradas com um ponto`;`e vírgula ().</span><span class="sxs-lookup"><span data-stu-id="30c11-216">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="30c11-217">No exemplo a seguir, a folha de estilo *lib. css* na pasta *wwwroot* não é considerada um ativo estático e não está incluída no RCL publicado:</span><span class="sxs-lookup"><span data-stu-id="30c11-217">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="30c11-218">Integração do typescript</span><span class="sxs-lookup"><span data-stu-id="30c11-218">Typescript integration</span></span>

<span data-ttu-id="30c11-219">Para incluir arquivos TypeScript em um RCL:</span><span class="sxs-lookup"><span data-stu-id="30c11-219">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="30c11-220">Coloque os arquivos TypeScript ( *. TS*) fora da pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="30c11-220">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="30c11-221">Por exemplo, coloque os arquivos em uma pasta de *cliente* .</span><span class="sxs-lookup"><span data-stu-id="30c11-221">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="30c11-222">Configure a saída de compilação do TypeScript para a pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="30c11-222">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="30c11-223">Defina a `TypescriptOutDir` Propriedade dentro de um `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="30c11-223">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="30c11-224">Inclua o destino TypeScript como uma dependência do `ResolveCurrentProjectStaticWebAssets` destino adicionando o seguinte destino dentro de um `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="30c11-224">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="30c11-225">Consumir conteúdo de um RCL referenciado</span><span class="sxs-lookup"><span data-stu-id="30c11-225">Consume content from a referenced RCL</span></span>

<span data-ttu-id="30c11-226">Os arquivos incluídos na pasta *wwwroot* do RCL são expostos ao aplicativo de consumo sob o prefixo `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="30c11-226">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="30c11-227">Por exemplo, uma biblioteca chamada *Razor. class. lib* resulta em um caminho para o conteúdo estático `_content/Razor.Class.Lib/`em.</span><span class="sxs-lookup"><span data-stu-id="30c11-227">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="30c11-228">O aplicativo de consumo faz referência a ativos estáticos fornecidos `<script>`pela `<style>`biblioteca `<img>`com,, e outras marcas HTML.</span><span class="sxs-lookup"><span data-stu-id="30c11-228">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="30c11-229">O aplicativo de consumo deve ter o [suporte a arquivo estático](xref:fundamentals/static-files) habilitado em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="30c11-229">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="30c11-230">Ao executar o aplicativo de consumo da saída da`dotnet run`compilação (), os ativos da Web estáticos são habilitados por padrão no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="30c11-230">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="30c11-231">Para dar suporte a ativos em outros ambientes ao executar a partir da `UseStaticWebAssets` saída da compilação, chame no construtor de hosts em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="30c11-231">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="30c11-232">A `UseStaticWebAssets` chamada não é necessária ao executar um aplicativo da saída`dotnet publish`publicada ().</span><span class="sxs-lookup"><span data-stu-id="30c11-232">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="30c11-233">Fluxo de desenvolvimento de vários projetos</span><span class="sxs-lookup"><span data-stu-id="30c11-233">Multi-project development flow</span></span>

<span data-ttu-id="30c11-234">Quando o aplicativo de consumo for executado:</span><span class="sxs-lookup"><span data-stu-id="30c11-234">When the consuming app runs:</span></span>

* <span data-ttu-id="30c11-235">Os ativos no RCL permanecem em suas pastas originais.</span><span class="sxs-lookup"><span data-stu-id="30c11-235">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="30c11-236">Os ativos não são movidos para o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="30c11-236">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="30c11-237">Qualquer alteração na pasta *wwwroot* do RCL é refletida no aplicativo de consumo depois que o RCL é recriado e sem recriar o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="30c11-237">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="30c11-238">Quando o RCL é criado, é produzido um manifesto que descreve os locais estáticos de ativos da Web.</span><span class="sxs-lookup"><span data-stu-id="30c11-238">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="30c11-239">O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados.</span><span class="sxs-lookup"><span data-stu-id="30c11-239">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="30c11-240">Quando um novo ativo é adicionado a um RCL, o RCL deve ser recriado para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.</span><span class="sxs-lookup"><span data-stu-id="30c11-240">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="30c11-241">Publicar</span><span class="sxs-lookup"><span data-stu-id="30c11-241">Publish</span></span>

<span data-ttu-id="30c11-242">Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes referenciados são copiados para a pasta *wwwroot* do `_content/{LIBRARY NAME}/`aplicativo publicado em.</span><span class="sxs-lookup"><span data-stu-id="30c11-242">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end
