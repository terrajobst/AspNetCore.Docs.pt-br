---
title: Introdução à identidade do ASP.NET Core
author: rick-anderson
description: Use a identidade com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 979681cfc196aca9fb5097583d99a086e1c597ba
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082462"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="58898-104">Introdução à identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58898-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="58898-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58898-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="58898-106">ASP.NET Core identidade é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58898-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="58898-107">Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="58898-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="58898-108">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="58898-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="58898-109">A identidade pode ser configurada usando um banco de dados de SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="58898-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="58898-110">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="58898-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="58898-111">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="58898-111">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="58898-112">Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="58898-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="58898-113">Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="58898-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="58898-114">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="58898-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="58898-115">O [AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="58898-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="58898-116">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="58898-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="58898-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="58898-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="58898-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="58898-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="58898-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="58898-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="58898-120">Consulte [AddDefaultIdentity Source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="58898-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="58898-121">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="58898-121">Create a Web app with authentication</span></span>

<span data-ttu-id="58898-122">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="58898-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58898-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58898-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58898-124">Selecione **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="58898-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="58898-125">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="58898-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="58898-126">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="58898-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="58898-127">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="58898-127">Click **OK**.</span></span>
* <span data-ttu-id="58898-128">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="58898-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="58898-129">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="58898-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58898-130">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="58898-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="58898-131">O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="58898-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="58898-132">A biblioteca de classes de identidade Razor expõe pontos de extremidade `Identity` com a área.</span><span class="sxs-lookup"><span data-stu-id="58898-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="58898-133">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="58898-133">For example:</span></span>

* <span data-ttu-id="58898-134">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="58898-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="58898-135">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="58898-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="58898-136">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="58898-136">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="58898-137">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="58898-137">Apply migrations</span></span>

<span data-ttu-id="58898-138">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="58898-138">Apply the migrations to initialise the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58898-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58898-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58898-140">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="58898-140">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58898-141">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="58898-141">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="58898-142">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="58898-142">Test Register and Login</span></span>

<span data-ttu-id="58898-143">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="58898-143">Run the app and register a user.</span></span> <span data-ttu-id="58898-144">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="58898-144">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="58898-145">Configurar serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="58898-145">Configure Identity services</span></span>

<span data-ttu-id="58898-146">Os serviços são adicionados `ConfigureServices`no.</span><span class="sxs-lookup"><span data-stu-id="58898-146">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="58898-147">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="58898-147">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="58898-148">O código anterior configura a identidade com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="58898-148">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="58898-149">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58898-149">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="58898-150">A identidade é habilitada chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="58898-150">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="58898-151">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="58898-151">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="58898-152">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58898-152">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="58898-153">A identidade é habilitada para o aplicativo `UseAuthentication` chamando `Configure` no método.</span><span class="sxs-lookup"><span data-stu-id="58898-153">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="58898-154">`UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="58898-154">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="58898-155">Esses serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58898-155">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="58898-156">A identidade é habilitada para o aplicativo `UseIdentity` chamando `Configure` no método.</span><span class="sxs-lookup"><span data-stu-id="58898-156">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="58898-157">`UseIdentity`Adiciona [middleware](xref:fundamentals/middleware/index) de autenticação baseada em cookie ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="58898-157">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="58898-158">Para obter mais informações, consulte a [classe identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="58898-158">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="58898-159">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="58898-159">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="58898-160">Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="58898-160">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="58898-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58898-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58898-162">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="58898-162">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="58898-163">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="58898-163">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="58898-164">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="58898-164">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="58898-165">Caso contrário, use o namespace correto para `ApplicationDbContext`o:</span><span class="sxs-lookup"><span data-stu-id="58898-165">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="58898-166">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="58898-166">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="58898-167">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="58898-167">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="58898-168">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="58898-168">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="58898-169">Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="58898-169">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="58898-170">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="58898-170">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="58898-171">`_userManager`é fornecido pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="58898-171">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="58898-172">Quando um usuário clica no link **registrar** , a `Register` ação é invocada `AccountController`em.</span><span class="sxs-lookup"><span data-stu-id="58898-172">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="58898-173">A `Register` ação cria o usuário chamando `CreateAsync` no `_userManager` objeto (fornecido para `AccountController` por injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="58898-173">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="58898-174">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="58898-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="58898-175">**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="58898-175">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="58898-176">Fazer Logon</span><span class="sxs-lookup"><span data-stu-id="58898-176">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="58898-177">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="58898-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="58898-178">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="58898-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="58898-179">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="58898-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="58898-180">Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="58898-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="58898-181">`PasswordSignInAsync`é chamado no `_signInManager` objeto (fornecido pela injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="58898-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="58898-182">A classe `Controller` base expõe uma `User` propriedade que você pode acessar por meio de métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="58898-182">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="58898-183">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="58898-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="58898-184">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="58898-184">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="58898-185">O formulário de logon é exibido quando os usuários selecionam o link **de logon** ou são redirecionados ao acessar uma página que requer autenticação.</span><span class="sxs-lookup"><span data-stu-id="58898-185">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="58898-186">Quando o usuário envia o formulário na página de login, a ação`AccountController` `Login` é chamada.</span><span class="sxs-lookup"><span data-stu-id="58898-186">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="58898-187">O `Login` faz chamadas `PasswordSignInAsync` no objeto `_signInManager` (fornecido pelo `AccountController` por injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="58898-187">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="58898-188">A classe base`Controller` ( `PageModel`ou) expõe uma `User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="58898-188">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="58898-189">Por exemplo, `User.Claims` pode ser enumerado para tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="58898-189">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="58898-190">Fazer logoff</span><span class="sxs-lookup"><span data-stu-id="58898-190">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="58898-191">O link **fazer logoff** invoca a `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="58898-191">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="58898-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="58898-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="58898-193">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58898-193">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="58898-194">Clicar no link **fazer logoff** chama a `LogOut` ação.</span><span class="sxs-lookup"><span data-stu-id="58898-194">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="58898-195">O código anterior chama o `_signInManager.SignOutAsync` método.</span><span class="sxs-lookup"><span data-stu-id="58898-195">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="58898-196">O `SignOutAsync` método limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="58898-196">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="58898-197">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="58898-197">Test Identity</span></span>

<span data-ttu-id="58898-198">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="58898-198">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="58898-199">Para testar a identidade, [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) adicione à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="58898-199">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="58898-200">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="58898-200">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="58898-201">Você será redirecionado para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="58898-201">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="58898-202">Explorar identidade</span><span class="sxs-lookup"><span data-stu-id="58898-202">Explore Identity</span></span>

<span data-ttu-id="58898-203">Para explorar a identidade com mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="58898-203">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="58898-204">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="58898-204">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="58898-205">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="58898-205">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="58898-206">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="58898-206">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="58898-207">Todos os pacotes NuGet dependentes de identidade estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="58898-207">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="58898-208">O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="58898-208">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="58898-209">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="58898-209">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="58898-210">Migrando para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58898-210">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="58898-211">Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="58898-211">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="58898-212">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="58898-212">Setting password strength</span></span>

<span data-ttu-id="58898-213">Consulte [configuração](#pw) para obter um exemplo que defina os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="58898-213">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="58898-214">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="58898-214">Next Steps</span></span>

* [<span data-ttu-id="58898-215">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="58898-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
