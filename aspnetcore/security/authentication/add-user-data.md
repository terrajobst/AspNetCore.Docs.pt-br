---
title: Adicionar, baixar e excluir dados de usuário à identidade em um projeto ASP.NET Core
author: rick-anderson
description: Saiba como adicionar dados personalizados do usuário a identidade em um projeto ASP.NET Core. Exclua dados por GDPR.
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: e08c02e2e5d4a429aae10c59e7ae3ea48c975067
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885550"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="56dc3-104">Adicionar, baixar e excluir dados de usuário personalizada à identidade em um projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56dc3-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="56dc3-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56dc3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="56dc3-106">Este artigo mostra como:</span><span class="sxs-lookup"><span data-stu-id="56dc3-106">This article shows how to:</span></span>

* <span data-ttu-id="56dc3-107">Adicione dados de usuário personalizada para um aplicativo web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="56dc3-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="56dc3-108">Marque o modelo de dados de usuário personalizado com o atributo <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> para que ele seja automaticamente disponibilizado para download e exclusão.</span><span class="sxs-lookup"><span data-stu-id="56dc3-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="56dc3-109">Tornando os dados capazes de ser baixado e excluído ajuda a cumprir [GDPR](xref:security/gdpr) requisitos.</span><span class="sxs-lookup"><span data-stu-id="56dc3-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="56dc3-110">O exemplo de projeto é criado a partir de um aplicativo web páginas Razor, mas as instruções são semelhantes para um aplicativo web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="56dc3-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="56dc3-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56dc3-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56dc3-112">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="56dc3-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="56dc3-113">Criar um aplicativo Web do Razor</span><span class="sxs-lookup"><span data-stu-id="56dc3-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56dc3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56dc3-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="56dc3-115">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="56dc3-116">Nomeie o projeto **WebApp1** se você deseja corresponder ao namespace da [baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) código.</span><span class="sxs-lookup"><span data-stu-id="56dc3-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="56dc3-117">Selecione **ASP.NET Core aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="56dc3-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="56dc3-118">Selecione **ASP.NET Core 3,0** na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="56dc3-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="56dc3-119">Selecione o **aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="56dc3-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="56dc3-120">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="56dc3-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="56dc3-121">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="56dc3-122">Nomeie o projeto **WebApp1** se você deseja corresponder ao namespace da [baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) código.</span><span class="sxs-lookup"><span data-stu-id="56dc3-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="56dc3-123">Selecione **ASP.NET Core aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="56dc3-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="56dc3-124">Selecione **ASP.NET Core 2,2** na lista suspensa</span><span class="sxs-lookup"><span data-stu-id="56dc3-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="56dc3-125">Selecione o **aplicativo Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="56dc3-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="56dc3-126">Compile e execute o projeto.</span><span class="sxs-lookup"><span data-stu-id="56dc3-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56dc3-127">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="56dc3-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="56dc3-128">Execute o scaffolder de identidade</span><span class="sxs-lookup"><span data-stu-id="56dc3-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56dc3-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56dc3-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56dc3-130">Partir **Gerenciador de soluções**, clique com botão direito no projeto > **Add** > **New Scaffolded Item**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="56dc3-131">No painel esquerdo da caixa de diálogo **Adicionar Scaffold** , selecione **identidade** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="56dc3-132">Na caixa de diálogo **Adicionar identidade** , as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="56dc3-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="56dc3-133">Selecione o arquivo de layout existente *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="56dc3-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="56dc3-134">Selecione os arquivos a seguir para substituir:</span><span class="sxs-lookup"><span data-stu-id="56dc3-134">Select the following files to override:</span></span>
    * <span data-ttu-id="56dc3-135">**Conta/registro**</span><span class="sxs-lookup"><span data-stu-id="56dc3-135">**Account/Register**</span></span>
    * <span data-ttu-id="56dc3-136">**Conta/gerenciar/índice**</span><span class="sxs-lookup"><span data-stu-id="56dc3-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="56dc3-137">Selecione o **+** botão para criar um novo **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="56dc3-138">Aceite o tipo (**WebApp1.Models.WebApp1Context** se o projeto é denominado **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="56dc3-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="56dc3-139">Selecione o **+** botão para criar um novo **classe User**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="56dc3-140">Aceite o tipo (**WebApp1User** se o projeto é denominado **WebApp1**) > **adicionar**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="56dc3-141">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="56dc3-141">Select **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56dc3-142">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="56dc3-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="56dc3-143">Se você já não tiver instalado o scaffolder de ASP.NET Core, instale-o agora:</span><span class="sxs-lookup"><span data-stu-id="56dc3-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="56dc3-144">Adicione uma referência de pacote ao [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) ao arquivo de projeto (. csproj).</span><span class="sxs-lookup"><span data-stu-id="56dc3-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="56dc3-145">Execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="56dc3-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="56dc3-146">Execute o seguinte comando para listar as opções de scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="56dc3-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="56dc3-147">Na pasta do projeto, execute o scaffolder de identidade:</span><span class="sxs-lookup"><span data-stu-id="56dc3-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="56dc3-148">Siga as instruções da [migrações, UseAuthentication e layout](xref:security/authentication/scaffold-identity#efm) para executar as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="56dc3-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="56dc3-149">Criar uma migração e atualizar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="56dc3-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="56dc3-150">Adicione `UseAuthentication` a `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="56dc3-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="56dc3-151">Adicionar `<partial name="_LoginPartial" />` ao arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="56dc3-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="56dc3-152">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="56dc3-152">Test the app:</span></span>
  * <span data-ttu-id="56dc3-153">Registrar um usuário</span><span class="sxs-lookup"><span data-stu-id="56dc3-153">Register a user</span></span>
  * <span data-ttu-id="56dc3-154">Selecione o novo nome de usuário (ao lado de **Logout** link).</span><span class="sxs-lookup"><span data-stu-id="56dc3-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="56dc3-155">Talvez você precise expandir a janela ou selecione o ícone da barra de navegação para mostrar o nome de usuário e outros links.</span><span class="sxs-lookup"><span data-stu-id="56dc3-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="56dc3-156">Selecione o **dados pessoais** guia.</span><span class="sxs-lookup"><span data-stu-id="56dc3-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="56dc3-157">Selecione o **Baixe** botão e examinado o *PersonalData.json* arquivo.</span><span class="sxs-lookup"><span data-stu-id="56dc3-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="56dc3-158">Teste o **excluir** botão, que exclui o fez logon de usuário.</span><span class="sxs-lookup"><span data-stu-id="56dc3-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="56dc3-159">Adicionar dados de usuário personalizada para o banco de dados de identidade</span><span class="sxs-lookup"><span data-stu-id="56dc3-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="56dc3-160">Atualização de `IdentityUser` derivado da classe com propriedades personalizadas.</span><span class="sxs-lookup"><span data-stu-id="56dc3-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="56dc3-161">Se você nomeou o projeto WebApp1, o arquivo é nomeado *Areas/Identity/Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="56dc3-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="56dc3-162">Atualize o arquivo com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="56dc3-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="56dc3-163">As propriedades com o atributo [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) são:</span><span class="sxs-lookup"><span data-stu-id="56dc3-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="56dc3-164">Excluído, quando o *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* página do Razor chama `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="56dc3-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="56dc3-165">Incluído nos dados baixados pela *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* página do Razor.</span><span class="sxs-lookup"><span data-stu-id="56dc3-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="56dc3-166">Atualizar a página Account/Manage/Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="56dc3-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="56dc3-167">Atualizar o `InputModel` na *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="56dc3-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="56dc3-168">Atualizar o *Areas/Identity/Pages/Account/Manage/Index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="56dc3-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="56dc3-169">Atualizar o *Areas/Identity/Pages/Account/Manage/Index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="56dc3-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="56dc3-170">Atualizar a página Register</span><span class="sxs-lookup"><span data-stu-id="56dc3-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="56dc3-171">Atualizar o `InputModel` na *Areas/Identity/Pages/Account/Register.cshtml.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="56dc3-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="56dc3-172">Atualizar o *Areas/Identity/Pages/Account/Register.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="56dc3-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="56dc3-173">Atualizar o *Areas/Identity/Pages/Account/Register.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="56dc3-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="56dc3-174">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="56dc3-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="56dc3-175">Adicionar uma migração para os dados de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="56dc3-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56dc3-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56dc3-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56dc3-177">No Visual Studio **Package Manager Console**:</span><span class="sxs-lookup"><span data-stu-id="56dc3-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="56dc3-178">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="56dc3-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="56dc3-179">Teste de criar, exibir, baixar, excluir dados de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="56dc3-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="56dc3-180">Teste o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="56dc3-180">Test the app:</span></span>

* <span data-ttu-id="56dc3-181">Registre um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="56dc3-181">Register a new user.</span></span>
* <span data-ttu-id="56dc3-182">Exibir os dados de usuário personalizada no `/Identity/Account/Manage` página.</span><span class="sxs-lookup"><span data-stu-id="56dc3-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="56dc3-183">Baixar e exibir os dados pessoais de usuários da `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="56dc3-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
