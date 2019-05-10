---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/07/2018
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 7ec36cc8f4832fb1e1a50831dfcb88f3cafb5ca9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895283"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="43223-103">Criar a interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43223-103">Create reusable UI using the Razor Class Library project in ASP.NET Core</span></span>

<span data-ttu-id="43223-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="43223-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="43223-105">Modos de exibição do Razor, páginas, controladores, modelos de página, [Componentes de exibição](xref:mvc/views/view-components) e modelos de dados podem ser inseridos em uma RCL (Biblioteca de Classes Razor).</span><span class="sxs-lookup"><span data-stu-id="43223-105">Razor views, pages, controllers, page models, [View components](xref:mvc/views/view-components), and data models can be built into a Razor Class Library (RCL).</span></span> <span data-ttu-id="43223-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="43223-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="43223-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="43223-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="43223-108">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="43223-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="43223-109">Este recurso requer [!INCLUDE[](~/includes/2.1-SDK.md)]</span><span class="sxs-lookup"><span data-stu-id="43223-109">This feature requires [!INCLUDE[](~/includes/2.1-SDK.md)]</span></span>

<span data-ttu-id="43223-110">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43223-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="43223-111">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="43223-111">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43223-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43223-112">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="43223-113">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="43223-113">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="43223-114">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="43223-114">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="43223-115">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="43223-115">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="43223-116">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="43223-116">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="43223-117">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="43223-117">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="43223-118">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="43223-118">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="43223-119">Uma biblioteca de classes Razor tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="43223-119">A Razor Class Library has the following project file:</span></span>

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43223-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="43223-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43223-121">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="43223-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="43223-122">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="43223-122">For example:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="43223-123">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="43223-123">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="43223-124">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="43223-124">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="43223-125">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="43223-125">Add Razor files to the RCL.</span></span>

<span data-ttu-id="43223-126">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="43223-126">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="43223-127">Ver [layout de páginas RCL](#afs) para criar uma RCL que expõe conteúdo em `~/Pages` em vez de `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="43223-127">See [RCL Pages layout](#afs) to create a RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="referencing-razor-class-library-content"></a><span data-ttu-id="43223-128">Referenciando o conteúdo da Biblioteca de Classes Razor</span><span class="sxs-lookup"><span data-stu-id="43223-128">Referencing Razor Class Library content</span></span>

<span data-ttu-id="43223-129">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="43223-129">The RCL can be referenced by:</span></span>

* <span data-ttu-id="43223-130">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="43223-130">NuGet package.</span></span> <span data-ttu-id="43223-131">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="43223-131">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="43223-132">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="43223-132">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="43223-133">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="43223-133">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-a-razor-class-library-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="43223-134">Passo a passo: Criar um projeto de biblioteca de classes Razor e usar a partir de um projeto de páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="43223-134">Walkthrough: Create a Razor Class Library project and use from a Razor Pages project</span></span>

<span data-ttu-id="43223-135">Você pode baixar o [projeto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="43223-135">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="43223-136">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="43223-136">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="43223-137">Você pode deixar comentários [nesse problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="43223-137">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="43223-138">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="43223-138">Test the download app</span></span>

<span data-ttu-id="43223-139">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-a-razor-class-library).</span><span class="sxs-lookup"><span data-stu-id="43223-139">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-a-razor-class-library).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43223-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43223-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43223-141">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="43223-141">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="43223-142">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43223-142">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43223-143">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="43223-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43223-144">Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="43223-144">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```console
dotnet build
```

<span data-ttu-id="43223-145">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="43223-145">Move to the *WebApp1* directory and run the app:</span></span>

```console
dotnet run
```

---

<span data-ttu-id="43223-146">Siga as instruções em [Testar WebApp1](#test)</span><span class="sxs-lookup"><span data-stu-id="43223-146">Follow the instructions in [Test WebApp1](#test)</span></span>

## <a name="create-a-razor-class-library"></a><span data-ttu-id="43223-147">Criar uma Biblioteca de Classes Razor</span><span class="sxs-lookup"><span data-stu-id="43223-147">Create a Razor Class Library</span></span>

<span data-ttu-id="43223-148">Nesta seção, uma RCL (Biblioteca de Classes Razor) é criada.</span><span class="sxs-lookup"><span data-stu-id="43223-148">In this section, a Razor Class Library (RCL) is created.</span></span> <span data-ttu-id="43223-149">Arquivos Razor são adicionados à RCL.</span><span class="sxs-lookup"><span data-stu-id="43223-149">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43223-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43223-150">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43223-151">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="43223-151">Create the RCL project:</span></span>

* <span data-ttu-id="43223-152">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="43223-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="43223-153">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="43223-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="43223-154">Nomeie o aplicativo **RazorUIClassLib** > **Okey**.</span><span class="sxs-lookup"><span data-stu-id="43223-154">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="43223-155">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="43223-155">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="43223-156">Selecione **Biblioteca de Classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="43223-156">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="43223-157">Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="43223-157">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43223-158">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="43223-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43223-159">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="43223-159">From the command line, run the following:</span></span>

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="43223-160">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="43223-160">The preceding commands:</span></span>

* <span data-ttu-id="43223-161">Criam a RCL (Biblioteca de Classes Razor) `RazorUIClassLib`.</span><span class="sxs-lookup"><span data-stu-id="43223-161">Creates the `RazorUIClassLib` Razor Class Library (RCL).</span></span>
* <span data-ttu-id="43223-162">Criam uma página Razor _Message e a adicionam à RCL.</span><span class="sxs-lookup"><span data-stu-id="43223-162">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="43223-163">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="43223-163">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="43223-164">Cria uma [viewstart](xref:mvc/views/layout#running-code-before-each-view) de arquivo e o adiciona à RCL.</span><span class="sxs-lookup"><span data-stu-id="43223-164">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="43223-165">O *viewstart* arquivo é necessário para usar o layout do projeto páginas Razor (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="43223-165">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="43223-166">Adicionar pastas e arquivos Razor ao projeto</span><span class="sxs-lookup"><span data-stu-id="43223-166">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="43223-167">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="43223-167">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="43223-168">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="43223-168">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

<span data-ttu-id="43223-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="43223-169">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="43223-170">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="43223-170">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="43223-171">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="43223-171">For example:</span></span>

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="43223-172">Para obter mais informações sobre *viewimports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="43223-172">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="43223-173">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="43223-173">Build the class library to verify there are no compiler errors:</span></span>

```console
dotnet build RazorUIClassLib
```

<span data-ttu-id="43223-174">A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="43223-174">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="43223-175">*RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="43223-175">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="43223-176">Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="43223-176">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="43223-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43223-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="43223-178">Crie aplicativo Web Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="43223-178">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="43223-179">No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução > **Adicionar** >  **Novo Projeto**.</span><span class="sxs-lookup"><span data-stu-id="43223-179">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="43223-180">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="43223-180">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="43223-181">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="43223-181">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="43223-182">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="43223-182">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="43223-183">Selecione **Aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="43223-183">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="43223-184">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="43223-184">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="43223-185">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Dependências de Build** > **Dependências do Projeto**.</span><span class="sxs-lookup"><span data-stu-id="43223-185">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="43223-186">Marque **RazorUIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="43223-186">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="43223-187">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** > **Referência**.</span><span class="sxs-lookup"><span data-stu-id="43223-187">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="43223-188">Na caixa de diálogo **Gerenciador de Referências**, marque **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="43223-188">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="43223-189">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43223-189">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="43223-190">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="43223-190">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="43223-191">Crie um aplicativo Web Páginas Razor e um arquivo de solução contendo o aplicativo Páginas Razor e a Biblioteca de Classes Razor:</span><span class="sxs-lookup"><span data-stu-id="43223-191">Create a Razor Pages web app and a solution file containing the Razor Pages app and the Razor Class Library:</span></span>

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="43223-192">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="43223-192">Build and run the web app:</span></span>

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a><span data-ttu-id="43223-193">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="43223-193">Test WebApp1</span></span>

<span data-ttu-id="43223-194">Verifique se a biblioteca de classes da interface do usuário do Razor está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="43223-194">Verify the Razor UI class library is being used.</span></span>

* <span data-ttu-id="43223-195">Navegue para `/MyFeature/Page1`.</span><span class="sxs-lookup"><span data-stu-id="43223-195">Browse to `/MyFeature/Page1`.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="43223-196">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="43223-196">Override views, partial views, and pages</span></span>

<span data-ttu-id="43223-197">Quando uma exibição, uma exibição parcial ou uma Página Razor for encontrada no aplicativo Web e na Biblioteca de Classes Razor, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="43223-197">When a view, partial view, or Razor Page is found in both the web app and the Razor Class Library, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="43223-198">Por exemplo, adicione *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ao WebApp1, e a Page1 ao WebApp1 terá precedência sobre a Page1 na biblioteca de classes Razor.</span><span class="sxs-lookup"><span data-stu-id="43223-198">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the Razor Class Library.</span></span>

<span data-ttu-id="43223-199">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="43223-199">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="43223-200">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="43223-200">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="43223-201">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="43223-201">Update the markup to indicate the new location.</span></span> <span data-ttu-id="43223-202">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="43223-202">Build and run the app to verify the app's version of the partial is being used.</span></span>

<a name="afs"></a>

### <a name="rcl-pages-layout"></a><span data-ttu-id="43223-203">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="43223-203">RCL Pages layout</span></span>

<span data-ttu-id="43223-204">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="43223-204">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="43223-205">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="43223-205">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="43223-206">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="43223-206">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="43223-207">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="43223-207">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="43223-208">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="43223-208">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```
