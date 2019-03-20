---
title: Introdução à identidade do ASP.NET Core
author: rick-anderson
description: Use identidade com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e muito mais).
ms.author: riande
ms.date: 08/08/2018
uid: security/authentication/identity
ms.openlocfilehash: 355c53e0c957944cb35c37c6b01e724af5f93f44
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265465"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="5eb68-104">Introdução à identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb68-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="5eb68-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5eb68-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5eb68-106">O ASP.NET Core Identity é um sistema de associação que adiciona a funcionalidade de logon para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5eb68-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="5eb68-107">Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou eles podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="5eb68-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5eb68-108">Provedores de logon externo com suporte incluem [Facebook, Google, Account da Microsoft e Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5eb68-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5eb68-109">Identidade pode ser configurada usando um banco de dados do SQL Server para armazenar nomes de usuário, senhas e dados de perfil.</span><span class="sxs-lookup"><span data-stu-id="5eb68-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5eb68-110">Como alternativa, outro repositório persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="5eb68-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5eb68-111">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5eb68-111">[View or download the sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="5eb68-112">Neste tópico, saiba como usar a identidade para registrar, faça logon e logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="5eb68-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5eb68-113">Para obter instruções mais detalhadas sobre como criar aplicativos que usam a identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="5eb68-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="5eb68-114">AddDefaultIdentity e AddIdentity</span><span class="sxs-lookup"><span data-stu-id="5eb68-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5eb68-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) foi introduzido no ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="5eb68-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5eb68-116">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="5eb68-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="5eb68-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="5eb68-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="5eb68-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="5eb68-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="5eb68-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="5eb68-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="5eb68-120">Ver [AddDefaultIdentity fonte](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="5eb68-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5eb68-121">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="5eb68-121">Create a Web app with authentication</span></span>

<span data-ttu-id="5eb68-122">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="5eb68-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5eb68-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5eb68-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5eb68-124">Selecione **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="5eb68-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="5eb68-125">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="5eb68-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5eb68-126">Nomeie o projeto **WebApp1** para ter o mesmo namespace que o download do projeto.</span><span class="sxs-lookup"><span data-stu-id="5eb68-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5eb68-127">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="5eb68-127">Click **OK**.</span></span>
* <span data-ttu-id="5eb68-128">Selecione um ASP.NET Core **aplicativo Web**, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="5eb68-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="5eb68-129">Selecione **contas de usuário individuais** e clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="5eb68-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5eb68-130">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb68-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="5eb68-131">Fornece o projeto gerado [ASP.NET Core Identity](xref:security/authentication/identity) como um [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="5eb68-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5eb68-132">A biblioteca de classes Razor identidade expõe pontos de extremidade com o `Identity` área.</span><span class="sxs-lookup"><span data-stu-id="5eb68-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5eb68-133">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5eb68-133">For example:</span></span>

* <span data-ttu-id="5eb68-134">Identidade/logon/conta</span><span class="sxs-lookup"><span data-stu-id="5eb68-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="5eb68-135">Identidade/logoff/conta</span><span class="sxs-lookup"><span data-stu-id="5eb68-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5eb68-136">/ Identidade/conta/gerenciar</span><span class="sxs-lookup"><span data-stu-id="5eb68-136">/Identity/Account/Manage</span></span>

### <a name="test-register-and-login"></a><span data-ttu-id="5eb68-137">Registro de teste e de logon</span><span class="sxs-lookup"><span data-stu-id="5eb68-137">Test Register and Login</span></span>

<span data-ttu-id="5eb68-138">Execute o aplicativo e registrar um usuário.</span><span class="sxs-lookup"><span data-stu-id="5eb68-138">Run the app and register a user.</span></span> <span data-ttu-id="5eb68-139">Dependendo do tamanho da tela, você talvez precise selecionar o botão de alternância de navegação para ver os **registre** e **Login** links.</span><span class="sxs-lookup"><span data-stu-id="5eb68-139">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="5eb68-140">Configurar os serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="5eb68-140">Configure Identity services</span></span>

<span data-ttu-id="5eb68-141">Serviços são adicionados no `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5eb68-141">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5eb68-142">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="5eb68-142">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="5eb68-143">O código anterior configura a identidade com os valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="5eb68-143">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="5eb68-144">Serviços são disponibilizados para o aplicativo por meio [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5eb68-144">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="5eb68-145">Identidade é habilitada por meio da chamada [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="5eb68-145">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="5eb68-146">`UseAuthentication` Adiciona a autenticação [middleware](xref:fundamentals/middleware/index) ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="5eb68-146">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="5eb68-147">Serviços são disponibilizados para o aplicativo por meio [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5eb68-147">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="5eb68-148">Identidade é habilitada para o aplicativo, chamando `UseAuthentication` no `Configure` método.</span><span class="sxs-lookup"><span data-stu-id="5eb68-148">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="5eb68-149">`UseAuthentication` Adiciona a autenticação [middleware](xref:fundamentals/middleware/index) ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="5eb68-149">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="5eb68-150">Esses serviços são disponibilizados para o aplicativo por meio [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5eb68-150">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="5eb68-151">Identidade é habilitada para o aplicativo, chamando `UseIdentity` no `Configure` método.</span><span class="sxs-lookup"><span data-stu-id="5eb68-151">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="5eb68-152">`UseIdentity` Adiciona a autenticação baseada em cookie [middleware](xref:fundamentals/middleware/index) ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="5eb68-152">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="5eb68-153">Para obter mais informações, consulte o [classe IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="5eb68-153">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5eb68-154">Registre-se de Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="5eb68-154">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="5eb68-155">Siga as [criar o scaffolding de identidade em um projeto do Razor com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="5eb68-155">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5eb68-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5eb68-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5eb68-157">Adicione os arquivos de registro, logon e logoff.</span><span class="sxs-lookup"><span data-stu-id="5eb68-157">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5eb68-158">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb68-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5eb68-159">Se você criou o projeto com o nome **WebApp1**, execute os seguintes comandos.</span><span class="sxs-lookup"><span data-stu-id="5eb68-159">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="5eb68-160">Caso contrário, use o namespace correto para o `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="5eb68-160">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5eb68-161">O PowerShell usa o ponto e vírgula como separador de comando.</span><span class="sxs-lookup"><span data-stu-id="5eb68-161">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5eb68-162">Ao usar o PowerShell, a ponto e vírgula na lista de arquivos de escape ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="5eb68-162">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5eb68-163">Registre-se de examinar</span><span class="sxs-lookup"><span data-stu-id="5eb68-163">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="5eb68-164">Quando um usuário clica o **registre** link, o `RegisterModel.OnPostAsync` ação é invocada.</span><span class="sxs-lookup"><span data-stu-id="5eb68-164">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5eb68-165">O usuário é criado pelo [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sobre o `_userManager` objeto.</span><span class="sxs-lookup"><span data-stu-id="5eb68-165">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="5eb68-166">`_userManager` é fornecido pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="5eb68-166">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="5eb68-167">Quando um usuário clica o **registre** link, o `Register` ação é invocada em `AccountController`.</span><span class="sxs-lookup"><span data-stu-id="5eb68-167">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="5eb68-168">O `Register` ação cria o usuário chamando `CreateAsync` sobre o `_userManager` objeto (fornecidas para `AccountController` pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="5eb68-168">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="5eb68-169">Se o usuário foi criado com êxito, o usuário é conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="5eb68-169">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="5eb68-170">**Observação:** Ver [confirmação de conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas impedir que o logon imediata no momento do registro.</span><span class="sxs-lookup"><span data-stu-id="5eb68-170">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5eb68-171">Fazer Logon</span><span class="sxs-lookup"><span data-stu-id="5eb68-171">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5eb68-172">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="5eb68-172">The Login form is displayed when:</span></span>

* <span data-ttu-id="5eb68-173">O **faça logon no** link é selecionado.</span><span class="sxs-lookup"><span data-stu-id="5eb68-173">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5eb68-174">Um usuário tenta acessar uma página restrita que eles não estão autorizados a acessar **ou** quando eles ainda não foram autenticados pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="5eb68-174">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5eb68-175">Quando o formulário na página de logon é enviado, o `OnPostAsync` ação é chamada.</span><span class="sxs-lookup"><span data-stu-id="5eb68-175">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5eb68-176">`PasswordSignInAsync` é chamado de `_signInManager` (fornecido pela injeção de dependência) do objeto.</span><span class="sxs-lookup"><span data-stu-id="5eb68-176">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="5eb68-177">A base `Controller` classe expõe um `User` propriedade que você pode acessar métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="5eb68-177">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="5eb68-178">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="5eb68-178">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="5eb68-179">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="5eb68-179">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5eb68-180">O formulário de logon é exibido quando os usuários selecionam o **faça logon no** vincular ou será redirecionado ao acessar uma página que requer autenticação.</span><span class="sxs-lookup"><span data-stu-id="5eb68-180">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="5eb68-181">Quando o usuário envia o formulário na página de login, a ação`AccountController` `Login` é chamada.</span><span class="sxs-lookup"><span data-stu-id="5eb68-181">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="5eb68-182">O `Login` faz chamadas `PasswordSignInAsync` no objeto `_signInManager` (fornecido pelo `AccountController` por injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="5eb68-182">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="5eb68-183">A base de (`Controller` ou `PageModel`) classe expõe um `User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="5eb68-183">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="5eb68-184">Por exemplo, `User.Claims` podem ser enumeradas para tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="5eb68-184">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="5eb68-185">Fazer logoff</span><span class="sxs-lookup"><span data-stu-id="5eb68-185">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5eb68-186">O **fazer logoff** link invoca o `LogoutModel.OnPost` ação.</span><span class="sxs-lookup"><span data-stu-id="5eb68-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="5eb68-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="5eb68-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span> <span data-ttu-id="5eb68-188">Não redirecionar após chamar `SignOutAsync` ou o usuário irá **não** ser desconectado.</span><span class="sxs-lookup"><span data-stu-id="5eb68-188">Don't redirect after calling `SignOutAsync` or the user will **not** be signed out.</span></span>

<span data-ttu-id="5eb68-189">POST é especificado na *Pages/Shared/_LoginPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb68-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="5eb68-190">Clicar a **fazer logoff** vincular chamadas a `LogOut` ação.</span><span class="sxs-lookup"><span data-stu-id="5eb68-190">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="5eb68-191">O código anterior chama o `_signInManager.SignOutAsync` método.</span><span class="sxs-lookup"><span data-stu-id="5eb68-191">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="5eb68-192">O `SignOutAsync` método limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="5eb68-192">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="5eb68-193">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="5eb68-193">Test Identity</span></span>

<span data-ttu-id="5eb68-194">Os modelos de projeto da web padrão permitem acesso anônimo para as home pages.</span><span class="sxs-lookup"><span data-stu-id="5eb68-194">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5eb68-195">Para testar a identidade, adicione [ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) para a página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="5eb68-195">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="5eb68-196">Se você estiver entrado, saia do serviço. Execute o aplicativo e selecione o **privacidade** link.</span><span class="sxs-lookup"><span data-stu-id="5eb68-196">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5eb68-197">Você será redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="5eb68-197">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="5eb68-198">Explore a identidade</span><span class="sxs-lookup"><span data-stu-id="5eb68-198">Explore Identity</span></span>

<span data-ttu-id="5eb68-199">Para explorar a identidade em mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="5eb68-199">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5eb68-200">Criar fonte de interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="5eb68-200">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5eb68-201">Examine a origem de cada página e percorrer o depurador.</span><span class="sxs-lookup"><span data-stu-id="5eb68-201">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="5eb68-202">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="5eb68-202">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5eb68-203">Todos os identidade NuGet pacotes dependentes são incluídos na [metapacote Microsoft](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5eb68-203">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="5eb68-204">O pacote principal para a identidade está [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="5eb68-204">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5eb68-205">Este pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído por `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="5eb68-205">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="5eb68-206">Migrando para o ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="5eb68-206">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5eb68-207">Para obter mais informações e diretrizes sobre como migrar o armazenamento de identidade existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="5eb68-207">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5eb68-208">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="5eb68-208">Setting password strength</span></span>

<span data-ttu-id="5eb68-209">Consulte [configuração](#pw) para obter um exemplo que defina os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="5eb68-209">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5eb68-210">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="5eb68-210">Next Steps</span></span>

* [<span data-ttu-id="5eb68-211">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="5eb68-211">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
