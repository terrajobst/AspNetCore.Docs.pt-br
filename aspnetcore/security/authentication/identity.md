---
title: Introdução à identidade no ASP.NET Core
author: rick-anderson
description: Use a identidade com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 10/15/2019
uid: security/authentication/identity
ms.openlocfilehash: 8da13ca5f74a9c829eb8137d33af0684ff88266d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333569"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="f8603-104">Introdução à identidade no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8603-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8603-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f8603-106">ASP.NET Core identidade é um sistema de associação que dá suporte à funcionalidade de logon da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="f8603-106">ASP.NET Core Identity is a membership system that supports user interface (UI) login functionality.</span></span> <span data-ttu-id="f8603-107">Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="f8603-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f8603-108">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f8603-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f8603-109">A identidade pode ser configurada usando um banco de dados de SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="f8603-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f8603-110">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="f8603-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f8603-111">Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="f8603-111">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f8603-112">Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="f8603-112">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="f8603-113">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f8603-113">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f8603-114">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="f8603-114">Create a Web app with authentication</span></span>

<span data-ttu-id="f8603-115">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="f8603-115">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8603-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8603-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8603-117">Selecione **arquivo** > **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="f8603-117">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f8603-118">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f8603-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f8603-119">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="f8603-119">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f8603-120">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="f8603-120">Click **OK**.</span></span>
* <span data-ttu-id="f8603-121">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="f8603-121">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f8603-122">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="f8603-122">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f8603-123">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-123">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="f8603-124">O comando anterior cria um aplicativo Web Razor usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="f8603-124">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="f8603-125">Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f8603-125">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="f8603-126">O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f8603-126">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f8603-127">A biblioteca de classes de identidade Razor expõe pontos de extremidade com a área de `Identity`.</span><span class="sxs-lookup"><span data-stu-id="f8603-127">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f8603-128">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f8603-128">For example:</span></span>

* <span data-ttu-id="f8603-129">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f8603-129">/Identity/Account/Login</span></span>
* <span data-ttu-id="f8603-130">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f8603-130">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f8603-131">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f8603-131">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f8603-132">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="f8603-132">Apply migrations</span></span>

<span data-ttu-id="f8603-133">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f8603-133">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8603-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8603-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8603-135">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="f8603-135">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f8603-136">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f8603-137">As migrações não são necessárias nesta etapa ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="f8603-137">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="f8603-138">Para o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f8603-138">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f8603-139">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="f8603-139">Test Register and Login</span></span>

<span data-ttu-id="f8603-140">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="f8603-140">Run the app and register a user.</span></span> <span data-ttu-id="f8603-141">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="f8603-141">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f8603-142">Configurar serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="f8603-142">Configure Identity services</span></span>

<span data-ttu-id="f8603-143">Os serviços são adicionados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8603-143">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f8603-144">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f8603-144">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="f8603-145">O código realçado anterior configura a identidade com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="f8603-145">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="f8603-146">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f8603-146">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f8603-147">A identidade é habilitada chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="f8603-147">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="f8603-148">`UseAuthentication` adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="f8603-148">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="f8603-149">O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="f8603-149">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="f8603-150">`app.UseAuthorization` está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização.</span><span class="sxs-lookup"><span data-stu-id="f8603-150">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="f8603-151">`UseRouting`, `UseAuthentication`, `UseAuthorization` e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.</span><span class="sxs-lookup"><span data-stu-id="f8603-151">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="f8603-152">Para obter mais informações sobre `IdentityOptions` e `Startup`, consulte <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f8603-152">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f8603-153">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="f8603-153">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8603-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8603-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8603-155">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="f8603-155">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="f8603-156">Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="f8603-156">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f8603-157">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-157">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f8603-158">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="f8603-158">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f8603-159">Caso contrário, use o namespace correto para o `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="f8603-159">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f8603-160">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="f8603-160">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f8603-161">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="f8603-161">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="f8603-162">Para obter mais informações sobre a identidade do scaffolding, consulte [identidade do Scaffold em um projeto do Razor com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="f8603-162">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f8603-163">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="f8603-163">Examine Register</span></span>

<span data-ttu-id="f8603-164">Quando um usuário clica no link **registrar** , a ação `RegisterModel.OnPostAsync` é invocada.</span><span class="sxs-lookup"><span data-stu-id="f8603-164">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f8603-165">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no objeto `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="f8603-165">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="f8603-166">`_userManager` é fornecida pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="f8603-166">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="f8603-167">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8603-167">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f8603-168">Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="f8603-168">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f8603-169">Fazer Logon</span><span class="sxs-lookup"><span data-stu-id="f8603-169">Log in</span></span>

<span data-ttu-id="f8603-170">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="f8603-170">The Login form is displayed when:</span></span>

* <span data-ttu-id="f8603-171">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="f8603-171">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f8603-172">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="f8603-172">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f8603-173">Quando o formulário na página de logon é enviado, a ação de `OnPostAsync` é chamada.</span><span class="sxs-lookup"><span data-stu-id="f8603-173">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f8603-174">`PasswordSignInAsync` é chamado no objeto `_signInManager` (fornecido pela injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="f8603-174">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f8603-175">A classe base `Controller` expõe uma propriedade `User` que pode ser acessada por meio de métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="f8603-175">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="f8603-176">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="f8603-176">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="f8603-177">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="f8603-177">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f8603-178">Fazer logoff</span><span class="sxs-lookup"><span data-stu-id="f8603-178">Log out</span></span>

<span data-ttu-id="f8603-179">O link **logout** invoca a ação `LogoutModel.OnPost`.</span><span class="sxs-lookup"><span data-stu-id="f8603-179">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="f8603-180">No código anterior, o `return RedirectToPage();` de código precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.</span><span class="sxs-lookup"><span data-stu-id="f8603-180">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="f8603-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="f8603-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f8603-182">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8603-182">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="f8603-183">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="f8603-183">Test Identity</span></span>

<span data-ttu-id="f8603-184">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="f8603-184">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f8603-185">Para testar a identidade, adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span><span class="sxs-lookup"><span data-stu-id="f8603-185">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f8603-186">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="f8603-186">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f8603-187">Você será redirecionado para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="f8603-187">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f8603-188">Explorar identidade</span><span class="sxs-lookup"><span data-stu-id="f8603-188">Explore Identity</span></span>

<span data-ttu-id="f8603-189">Para explorar a identidade com mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="f8603-189">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f8603-190">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="f8603-190">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f8603-191">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="f8603-191">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="f8603-192">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="f8603-192">Identity Components</span></span>

<span data-ttu-id="f8603-193">Todos os pacotes NuGet dependentes de identidade estão incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="f8603-193">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="f8603-194">O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="f8603-194">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f8603-195">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="f8603-195">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f8603-196">Migrando para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-196">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f8603-197">Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f8603-197">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f8603-198">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="f8603-198">Setting password strength</span></span>

<span data-ttu-id="f8603-199">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="f8603-199">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f8603-200">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="f8603-200">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f8603-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f8603-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f8603-202">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f8603-202">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f8603-203">Consulte [AddDefaultIdentity Source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f8603-203">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f8603-204">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f8603-204">Next Steps</span></span>

* [<span data-ttu-id="f8603-205">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="f8603-205">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8603-206">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8603-206">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f8603-207">ASP.NET Core identidade é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8603-207">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="f8603-208">Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="f8603-208">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f8603-209">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f8603-209">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f8603-210">A identidade pode ser configurada usando um banco de dados de SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="f8603-210">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f8603-211">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="f8603-211">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f8603-212">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f8603-212">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f8603-213">Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="f8603-213">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f8603-214">Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="f8603-214">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f8603-215">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="f8603-215">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f8603-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f8603-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f8603-217">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f8603-217">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f8603-218">Consulte [AddDefaultIdentity Source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f8603-218">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f8603-219">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="f8603-219">Create a Web app with authentication</span></span>

<span data-ttu-id="f8603-220">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="f8603-220">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8603-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8603-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8603-222">Selecione **arquivo** > **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="f8603-222">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f8603-223">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f8603-223">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f8603-224">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="f8603-224">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f8603-225">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="f8603-225">Click **OK**.</span></span>
* <span data-ttu-id="f8603-226">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="f8603-226">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f8603-227">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="f8603-227">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f8603-228">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-228">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="f8603-229">O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f8603-229">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f8603-230">A biblioteca de classes de identidade Razor expõe pontos de extremidade com a área de `Identity`.</span><span class="sxs-lookup"><span data-stu-id="f8603-230">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f8603-231">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f8603-231">For example:</span></span>

* <span data-ttu-id="f8603-232">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f8603-232">/Identity/Account/Login</span></span>
* <span data-ttu-id="f8603-233">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f8603-233">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f8603-234">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f8603-234">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f8603-235">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="f8603-235">Apply migrations</span></span>

<span data-ttu-id="f8603-236">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f8603-236">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8603-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8603-237">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8603-238">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="f8603-238">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f8603-239">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f8603-240">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="f8603-240">Test Register and Login</span></span>

<span data-ttu-id="f8603-241">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="f8603-241">Run the app and register a user.</span></span> <span data-ttu-id="f8603-242">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="f8603-242">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f8603-243">Configurar serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="f8603-243">Configure Identity services</span></span>

<span data-ttu-id="f8603-244">Os serviços são adicionados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8603-244">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f8603-245">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="f8603-245">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="f8603-246">O código anterior configura a identidade com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="f8603-246">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f8603-247">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f8603-247">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f8603-248">A identidade é habilitada chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="f8603-248">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f8603-249">`UseAuthentication` adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="f8603-249">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="f8603-250">Para obter mais informações, consulte a [classe identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="f8603-250">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f8603-251">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="f8603-251">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="f8603-252">Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="f8603-252">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f8603-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8603-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8603-254">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="f8603-254">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f8603-255">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-255">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f8603-256">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="f8603-256">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f8603-257">Caso contrário, use o namespace correto para o `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="f8603-257">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f8603-258">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="f8603-258">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f8603-259">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="f8603-259">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f8603-260">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="f8603-260">Examine Register</span></span>

<span data-ttu-id="f8603-261">Quando um usuário clica no link **registrar** , a ação `RegisterModel.OnPostAsync` é invocada.</span><span class="sxs-lookup"><span data-stu-id="f8603-261">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f8603-262">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no objeto `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="f8603-262">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="f8603-263">`_userManager` é fornecida pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="f8603-263">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="f8603-264">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8603-264">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f8603-265">**Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="f8603-265">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f8603-266">Fazer Logon</span><span class="sxs-lookup"><span data-stu-id="f8603-266">Log in</span></span>

<span data-ttu-id="f8603-267">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="f8603-267">The Login form is displayed when:</span></span>

* <span data-ttu-id="f8603-268">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="f8603-268">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f8603-269">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="f8603-269">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f8603-270">Quando o formulário na página de logon é enviado, a ação de `OnPostAsync` é chamada.</span><span class="sxs-lookup"><span data-stu-id="f8603-270">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f8603-271">`PasswordSignInAsync` é chamado no objeto `_signInManager` (fornecido pela injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="f8603-271">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f8603-272">A classe base `Controller` expõe uma propriedade `User` que você pode acessar por meio de métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="f8603-272">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="f8603-273">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="f8603-273">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="f8603-274">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="f8603-274">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f8603-275">Fazer logoff</span><span class="sxs-lookup"><span data-stu-id="f8603-275">Log out</span></span>

<span data-ttu-id="f8603-276">O link **logout** invoca a ação `LogoutModel.OnPost`.</span><span class="sxs-lookup"><span data-stu-id="f8603-276">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="f8603-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="f8603-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f8603-278">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8603-278">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="f8603-279">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="f8603-279">Test Identity</span></span>

<span data-ttu-id="f8603-280">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="f8603-280">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f8603-281">Para testar a identidade, adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="f8603-281">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f8603-282">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="f8603-282">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f8603-283">Você será redirecionado para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="f8603-283">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f8603-284">Explorar identidade</span><span class="sxs-lookup"><span data-stu-id="f8603-284">Explore Identity</span></span>

<span data-ttu-id="f8603-285">Para explorar a identidade com mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="f8603-285">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f8603-286">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="f8603-286">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f8603-287">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="f8603-287">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="f8603-288">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="f8603-288">Identity Components</span></span>

<span data-ttu-id="f8603-289">Todos os pacotes NuGet dependentes de identidade estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f8603-289">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f8603-290">O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="f8603-290">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f8603-291">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="f8603-291">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f8603-292">Migrando para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8603-292">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f8603-293">Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="f8603-293">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f8603-294">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="f8603-294">Setting password strength</span></span>

<span data-ttu-id="f8603-295">Consulte [configuração](#pw) para um exemplo que define os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="f8603-295">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f8603-296">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f8603-296">Next Steps</span></span>

* [<span data-ttu-id="f8603-297">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="f8603-297">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
