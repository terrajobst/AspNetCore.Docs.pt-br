---
title: Adicionar, baixar e excluir dados de usuário para identidade em um projeto ASP.NET Core
author: rick-anderson
description: Saiba como adicionar dados de usuário personalizados à identidade em um projeto ASP.NET Core. Excluir dados por GDPR.
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 6daca5776930f80eec8d81132b5a5c4d4d5c13ad
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681156"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="4c41c-104">Adicionar, baixar e excluir dados de usuário personalizados para identidade em um projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c41c-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="4c41c-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4c41c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4c41c-106">Este artigo mostra como:</span><span class="sxs-lookup"><span data-stu-id="4c41c-106">This article shows how to:</span></span>

* <span data-ttu-id="4c41c-107">Adicione dados de usuário personalizados a um aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c41c-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="4c41c-108">Decorar o modelo de dados de usuário personalizado com o atributo <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> para que ele seja automaticamente disponibilizado para download e exclusão.</span><span class="sxs-lookup"><span data-stu-id="4c41c-108">Decorate the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="4c41c-109">Tornar os dados capazes de ser baixados e excluídos ajuda a atender aos requisitos de [GDPR](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="4c41c-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="4c41c-110">O exemplo de projeto é criado de um aplicativo Web Razor Pages, mas as instruções são semelhantes para um aplicativo Web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4c41c-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="4c41c-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c41c-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4c41c-112">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="4c41c-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="4c41c-113">Criar um aplicativo Web do Razor</span><span class="sxs-lookup"><span data-stu-id="4c41c-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c41c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c41c-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="4c41c-115">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="4c41c-116">Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="4c41c-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="4c41c-117">Selecione **ASP.NET Core aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="4c41c-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="4c41c-118">Selecione **ASP.NET Core 3,0** na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="4c41c-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="4c41c-119">Selecione o **aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="4c41c-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="4c41c-120">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="4c41c-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="4c41c-121">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="4c41c-122">Nomeie o projeto **WebApp1** se você quiser que ele corresponda ao namespace do código de [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="4c41c-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="4c41c-123">Selecione **ASP.NET Core aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="4c41c-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="4c41c-124">Selecione **ASP.NET Core 2,2** na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="4c41c-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="4c41c-125">Selecione o **aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="4c41c-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="4c41c-126">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="4c41c-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c41c-127">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4c41c-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="4c41c-128">Executar a identidade scaffolder</span><span class="sxs-lookup"><span data-stu-id="4c41c-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c41c-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c41c-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c41c-130">Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4c41c-131">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="4c41c-132">Na caixa de diálogo **Adicionar identidade** , as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="4c41c-132">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="4c41c-133">Selecione o arquivo de layout existente *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4c41c-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="4c41c-134">Selecione os seguintes arquivos a serem substituídos:</span><span class="sxs-lookup"><span data-stu-id="4c41c-134">Select the following files to override:</span></span>
    * <span data-ttu-id="4c41c-135">**Conta/registro**</span><span class="sxs-lookup"><span data-stu-id="4c41c-135">**Account/Register**</span></span>
    * <span data-ttu-id="4c41c-136">**Conta/gerenciamento/índice**</span><span class="sxs-lookup"><span data-stu-id="4c41c-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="4c41c-137">Selecione o botão **+** para criar uma nova **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="4c41c-138">Aceite o tipo (**WebApp1. Models. WebApp1Context** se o projeto for denominado **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="4c41c-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="4c41c-139">Selecione o botão **+** para criar uma nova **classe de usuário**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="4c41c-140">Aceite o tipo (**WebApp1User** se o projeto for nomeado **WebApp1**) > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="4c41c-141">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="4c41c-141">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c41c-142">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4c41c-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4c41c-143">Se você não tiver instalado o ASP.NET Core scaffolder anteriormente, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="4c41c-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="4c41c-144">Adicione uma referência de pacote a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (. csproj).</span><span class="sxs-lookup"><span data-stu-id="4c41c-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="4c41c-145">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="4c41c-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="4c41c-146">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="4c41c-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="4c41c-147">Na pasta do projeto, execute a identidade scaffolder:</span><span class="sxs-lookup"><span data-stu-id="4c41c-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="4c41c-148">Siga as instruções em [Migrations, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="4c41c-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="4c41c-149">Crie uma migração e atualize o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="4c41c-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="4c41c-150">Adicione `UseAuthentication` a `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="4c41c-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="4c41c-151">Adicione `<partial name="_LoginPartial" />` ao arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="4c41c-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="4c41c-152">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4c41c-152">Test the app:</span></span>
  * <span data-ttu-id="4c41c-153">Registrar um usuário</span><span class="sxs-lookup"><span data-stu-id="4c41c-153">Register a user</span></span>
  * <span data-ttu-id="4c41c-154">Selecione o novo nome de usuário (ao lado do link de **logout** ).</span><span class="sxs-lookup"><span data-stu-id="4c41c-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="4c41c-155">Talvez seja necessário expandir a janela ou selecionar o ícone da barra de navegação para mostrar o nome de usuário e outros links.</span><span class="sxs-lookup"><span data-stu-id="4c41c-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="4c41c-156">Selecione a guia **dados pessoais** .</span><span class="sxs-lookup"><span data-stu-id="4c41c-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="4c41c-157">Selecione o botão **baixar** e examinou o arquivo *PersonalData. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4c41c-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="4c41c-158">Teste o botão **excluir** , que exclui o usuário conectado.</span><span class="sxs-lookup"><span data-stu-id="4c41c-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="4c41c-159">Adicionar dados de usuário personalizados ao BD de identidade</span><span class="sxs-lookup"><span data-stu-id="4c41c-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="4c41c-160">Atualize a classe derivada `IdentityUser` com propriedades personalizadas.</span><span class="sxs-lookup"><span data-stu-id="4c41c-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="4c41c-161">Se você tiver nomeado o projeto WebApp1, o arquivo será denominado *areas/Identity/data/WebApp1User. cs*.</span><span class="sxs-lookup"><span data-stu-id="4c41c-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="4c41c-162">Atualize o arquivo com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4c41c-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="4c41c-163">Propriedades decoradas com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:</span><span class="sxs-lookup"><span data-stu-id="4c41c-163">Properties decorated with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="4c41c-164">Excluído quando a página de *áreas/identidade/páginas/conta/gerenciamento/DeletePersonalData. cshtml* do Razor chama `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="4c41c-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="4c41c-165">Incluídos nos dados baixados pela página do Razor *áreas/identidade/páginas/conta/gerenciamento/DownloadPersonalData. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4c41c-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="4c41c-166">Atualizar a página conta/gerenciar/index. cshtml</span><span class="sxs-lookup"><span data-stu-id="4c41c-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="4c41c-167">Atualize o `InputModel` em *áreas/identidade/páginas/conta/gerenciamento/índice. cshtml. cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="4c41c-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="4c41c-168">Atualize as *áreas/identidade/páginas/conta/gerenciamento/index. cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="4c41c-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="4c41c-169">Atualize as *áreas/identidade/páginas/conta/gerenciamento/index. cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="4c41c-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="4c41c-170">Atualizar a página Account/Register. cshtml</span><span class="sxs-lookup"><span data-stu-id="4c41c-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="4c41c-171">Atualize o `InputModel` em *áreas/identidade/páginas/conta/Register. cshtml. cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="4c41c-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="4c41c-172">Atualize as *áreas/identidade/páginas/conta/Register. cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="4c41c-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="4c41c-173">Atualize as *áreas/identidade/páginas/conta/Register. cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="4c41c-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="4c41c-174">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="4c41c-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="4c41c-175">Adicionar uma migração para os dados de usuário personalizados</span><span class="sxs-lookup"><span data-stu-id="4c41c-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4c41c-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c41c-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4c41c-177">No **console do Gerenciador de pacotes**do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="4c41c-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4c41c-178">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4c41c-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="4c41c-179">Testar criar, exibir, baixar, excluir dados de usuário personalizados</span><span class="sxs-lookup"><span data-stu-id="4c41c-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="4c41c-180">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4c41c-180">Test the app:</span></span>

* <span data-ttu-id="4c41c-181">Registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="4c41c-181">Register a new user.</span></span>
* <span data-ttu-id="4c41c-182">Exiba os dados de usuário personalizados na página `/Identity/Account/Manage`.</span><span class="sxs-lookup"><span data-stu-id="4c41c-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="4c41c-183">Baixe e exiba os dados pessoais dos usuários na página `/Identity/Account/Manage/PersonalData`.</span><span class="sxs-lookup"><span data-stu-id="4c41c-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
