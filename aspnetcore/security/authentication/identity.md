---
title: Introdução à identidade do ASP.NET Core
author: rick-anderson
description: Use a identidade com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 12/05/2019
uid: security/authentication/identity
ms.openlocfilehash: d4cda43c417a0d8e1b78f8b59b7050debf017269
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828224"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="91fbb-104">Introdução à identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91fbb-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91fbb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="91fbb-106">ASP.NET Core identidade:</span><span class="sxs-lookup"><span data-stu-id="91fbb-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="91fbb-107">É uma API que dá suporte à funcionalidade de logon da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="91fbb-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="91fbb-108">Gerencia usuários, senhas, dados de perfil, funções, declarações, tokens, confirmação por email e muito mais.</span><span class="sxs-lookup"><span data-stu-id="91fbb-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="91fbb-109">Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="91fbb-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="91fbb-110">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="91fbb-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="91fbb-111">O [código-fonte da identidade](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) está disponível no github.</span><span class="sxs-lookup"><span data-stu-id="91fbb-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="91fbb-112">[Scaffold identidade](xref:security/authentication/scaffold-identity) e exiba os arquivos gerados para examinar a interação do modelo com a identidade.</span><span class="sxs-lookup"><span data-stu-id="91fbb-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="91fbb-113">Normalmente, a identidade é configurada usando um banco de dados SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="91fbb-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="91fbb-114">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="91fbb-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="91fbb-115">Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="91fbb-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="91fbb-116">Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="91fbb-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="91fbb-117">A [plataforma Microsoft Identity](/azure/active-directory/develop/) é:</span><span class="sxs-lookup"><span data-stu-id="91fbb-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="91fbb-118">Uma evolução da plataforma de desenvolvedor do Azure Active Directory (AD do Azure).</span><span class="sxs-lookup"><span data-stu-id="91fbb-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="91fbb-119">Não relacionado à identidade de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91fbb-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="91fbb-120">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="91fbb-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="91fbb-121">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="91fbb-121">Create a Web app with authentication</span></span>

<span data-ttu-id="91fbb-122">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="91fbb-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91fbb-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91fbb-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91fbb-124">Selecione **arquivo** > **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="91fbb-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="91fbb-125">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="91fbb-126">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="91fbb-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="91fbb-127">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-127">Click **OK**.</span></span>
* <span data-ttu-id="91fbb-128">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="91fbb-129">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91fbb-130">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="91fbb-131">O comando anterior cria um aplicativo Web Razor usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="91fbb-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="91fbb-132">Para criar o aplicativo Web com o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="91fbb-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="91fbb-133">O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="91fbb-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="91fbb-134">A biblioteca de classes de identidade Razor expõe pontos de extremidade com a área de `Identity`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="91fbb-135">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="91fbb-135">For example:</span></span>

* <span data-ttu-id="91fbb-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="91fbb-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="91fbb-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="91fbb-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="91fbb-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="91fbb-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="91fbb-139">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="91fbb-139">Apply migrations</span></span>

<span data-ttu-id="91fbb-140">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="91fbb-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91fbb-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91fbb-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91fbb-142">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="91fbb-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91fbb-143">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="91fbb-144">As migrações não são necessárias nesta etapa ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="91fbb-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="91fbb-145">Para o LocalDB, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="91fbb-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="91fbb-146">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="91fbb-146">Test Register and Login</span></span>

<span data-ttu-id="91fbb-147">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="91fbb-147">Run the app and register a user.</span></span> <span data-ttu-id="91fbb-148">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="91fbb-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="91fbb-149">Configurar serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="91fbb-149">Configure Identity services</span></span>

<span data-ttu-id="91fbb-150">Os serviços são adicionados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="91fbb-151">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="91fbb-152">O código realçado anterior configura a identidade com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="91fbb-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="91fbb-153">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="91fbb-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="91fbb-154">A identidade é habilitada chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span><span class="sxs-lookup"><span data-stu-id="91fbb-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="91fbb-155">`UseAuthentication` adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="91fbb-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="91fbb-156">O aplicativo gerado por modelo não usa [autorização](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="91fbb-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="91fbb-157">`app.UseAuthorization` está incluído para garantir que ele seja adicionado na ordem correta caso o aplicativo adicione autorização.</span><span class="sxs-lookup"><span data-stu-id="91fbb-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="91fbb-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`e `UseEndpoints` devem ser chamados na ordem mostrada no código anterior.</span><span class="sxs-lookup"><span data-stu-id="91fbb-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="91fbb-159">Para obter mais informações sobre `IdentityOptions` e `Startup`, consulte <xref:Microsoft.AspNetCore.Identity.IdentityOptions> e [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="91fbb-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="91fbb-160">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="91fbb-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91fbb-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91fbb-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91fbb-162">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="91fbb-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="91fbb-163">Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="91fbb-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91fbb-164">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="91fbb-165">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="91fbb-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="91fbb-166">Caso contrário, use o namespace correto para o `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="91fbb-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="91fbb-167">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="91fbb-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="91fbb-168">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="91fbb-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="91fbb-169">Para obter mais informações sobre a identidade do scaffolding, consulte [identidade do Scaffold em um projeto do Razor com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="91fbb-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="91fbb-170">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="91fbb-170">Examine Register</span></span>

<span data-ttu-id="91fbb-171">Quando um usuário clica no link **registrar** , a ação `RegisterModel.OnPostAsync` é invocada.</span><span class="sxs-lookup"><span data-stu-id="91fbb-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="91fbb-172">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no objeto `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="91fbb-173">`_userManager` é fornecida pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="91fbb-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="91fbb-174">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="91fbb-175">Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="91fbb-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="91fbb-176">Fazer Logon</span><span class="sxs-lookup"><span data-stu-id="91fbb-176">Log in</span></span>

<span data-ttu-id="91fbb-177">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="91fbb-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="91fbb-178">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="91fbb-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="91fbb-179">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="91fbb-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="91fbb-180">Quando o formulário na página de logon é enviado, a ação de `OnPostAsync` é chamada.</span><span class="sxs-lookup"><span data-stu-id="91fbb-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="91fbb-181">`PasswordSignInAsync` é chamado no objeto `_signInManager` (fornecido pela injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="91fbb-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="91fbb-182">A classe base `Controller` expõe uma propriedade `User` que pode ser acessada por meio de métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="91fbb-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="91fbb-183">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="91fbb-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="91fbb-184">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="91fbb-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="91fbb-185">Logoff</span><span class="sxs-lookup"><span data-stu-id="91fbb-185">Log out</span></span>

<span data-ttu-id="91fbb-186">O link **logout** invoca a ação `LogoutModel.OnPost`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="91fbb-187">No código anterior, o `return RedirectToPage();` de código precisa ser um redirecionamento para que o navegador execute uma nova solicitação e a identidade do usuário seja atualizada.</span><span class="sxs-lookup"><span data-stu-id="91fbb-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="91fbb-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="91fbb-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="91fbb-189">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="91fbb-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="91fbb-190">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="91fbb-190">Test Identity</span></span>

<span data-ttu-id="91fbb-191">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="91fbb-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="91fbb-192">Para testar a identidade, adicione [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span><span class="sxs-lookup"><span data-stu-id="91fbb-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="91fbb-193">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="91fbb-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="91fbb-194">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="91fbb-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="91fbb-195">Explorar identidade</span><span class="sxs-lookup"><span data-stu-id="91fbb-195">Explore Identity</span></span>

<span data-ttu-id="91fbb-196">Para explorar a identidade com mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="91fbb-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="91fbb-197">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="91fbb-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="91fbb-198">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="91fbb-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="91fbb-199">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="91fbb-199">Identity Components</span></span>

<span data-ttu-id="91fbb-200">Todos os pacotes NuGet dependentes de identidade estão incluídos na [estrutura compartilhada ASP.NET Core](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="91fbb-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="91fbb-201">O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="91fbb-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="91fbb-202">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="91fbb-203">Migrando para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="91fbb-204">Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="91fbb-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="91fbb-205">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="91fbb-205">Setting password strength</span></span>

<span data-ttu-id="91fbb-206">Consulte [configuração](#pw) para obter um exemplo que defina os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="91fbb-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="91fbb-207">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="91fbb-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="91fbb-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="91fbb-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="91fbb-209">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="91fbb-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="91fbb-210">Consulte [AddDefaultIdentity Source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="91fbb-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="91fbb-211">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="91fbb-211">Next Steps</span></span>

* <span data-ttu-id="91fbb-212">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar a identidade usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="91fbb-212">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="91fbb-213">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="91fbb-213">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="91fbb-214">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91fbb-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="91fbb-215">ASP.NET Core identidade é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91fbb-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="91fbb-216">Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="91fbb-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="91fbb-217">Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="91fbb-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="91fbb-218">A identidade pode ser configurada usando um banco de dados de SQL Server para armazenar nomes de usuário, senhas e de perfil.</span><span class="sxs-lookup"><span data-stu-id="91fbb-218">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="91fbb-219">Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.</span><span class="sxs-lookup"><span data-stu-id="91fbb-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="91fbb-220">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="91fbb-220">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="91fbb-221">Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário.</span><span class="sxs-lookup"><span data-stu-id="91fbb-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="91fbb-222">Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.</span><span class="sxs-lookup"><span data-stu-id="91fbb-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="91fbb-223">AddDefaultIdentity e addidentity</span><span class="sxs-lookup"><span data-stu-id="91fbb-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="91fbb-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> foi introduzido no ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="91fbb-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="91fbb-225">Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="91fbb-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="91fbb-226">Consulte [AddDefaultIdentity Source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="91fbb-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="91fbb-227">Criar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="91fbb-227">Create a Web app with authentication</span></span>

<span data-ttu-id="91fbb-228">Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.</span><span class="sxs-lookup"><span data-stu-id="91fbb-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91fbb-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91fbb-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="91fbb-230">Selecione **arquivo** > **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="91fbb-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="91fbb-231">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="91fbb-232">Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto.</span><span class="sxs-lookup"><span data-stu-id="91fbb-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="91fbb-233">Clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-233">Click **OK**.</span></span>
* <span data-ttu-id="91fbb-234">Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="91fbb-235">Selecione **contas de usuário individuais** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="91fbb-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91fbb-236">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="91fbb-237">O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="91fbb-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="91fbb-238">A biblioteca de classes de identidade Razor expõe pontos de extremidade com a área de `Identity`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="91fbb-239">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="91fbb-239">For example:</span></span>

* <span data-ttu-id="91fbb-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="91fbb-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="91fbb-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="91fbb-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="91fbb-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="91fbb-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="91fbb-243">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="91fbb-243">Apply migrations</span></span>

<span data-ttu-id="91fbb-244">Aplique as migrações para inicializar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="91fbb-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91fbb-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91fbb-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91fbb-246">Execute o seguinte comando no console do Gerenciador de pacotes (PMC):</span><span class="sxs-lookup"><span data-stu-id="91fbb-246">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91fbb-247">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="91fbb-248">Teste de registro e logon</span><span class="sxs-lookup"><span data-stu-id="91fbb-248">Test Register and Login</span></span>

<span data-ttu-id="91fbb-249">Execute o aplicativo e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="91fbb-249">Run the app and register a user.</span></span> <span data-ttu-id="91fbb-250">Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .</span><span class="sxs-lookup"><span data-stu-id="91fbb-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="91fbb-251">Configurar serviços de identidade</span><span class="sxs-lookup"><span data-stu-id="91fbb-251">Configure Identity services</span></span>

<span data-ttu-id="91fbb-252">Os serviços são adicionados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="91fbb-253">O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="91fbb-254">O código anterior configura a identidade com valores de opção padrão.</span><span class="sxs-lookup"><span data-stu-id="91fbb-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="91fbb-255">Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="91fbb-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="91fbb-256">A identidade é habilitada chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="91fbb-256">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="91fbb-257">`UseAuthentication` adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="91fbb-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="91fbb-258">Para obter mais informações, consulte a [classe identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="91fbb-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="91fbb-259">Registro em Scaffold, logon e logoff</span><span class="sxs-lookup"><span data-stu-id="91fbb-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="91fbb-260">Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.</span><span class="sxs-lookup"><span data-stu-id="91fbb-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="91fbb-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91fbb-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="91fbb-262">Adicione os arquivos de registro, logon e LogOut.</span><span class="sxs-lookup"><span data-stu-id="91fbb-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="91fbb-263">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="91fbb-264">Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir.</span><span class="sxs-lookup"><span data-stu-id="91fbb-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="91fbb-265">Caso contrário, use o namespace correto para o `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="91fbb-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="91fbb-266">O PowerShell usa ponto e vírgula como um separador de comando.</span><span class="sxs-lookup"><span data-stu-id="91fbb-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="91fbb-267">Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="91fbb-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="91fbb-268">Examinar registro</span><span class="sxs-lookup"><span data-stu-id="91fbb-268">Examine Register</span></span>

<span data-ttu-id="91fbb-269">Quando um usuário clica no link **registrar** , a ação `RegisterModel.OnPostAsync` é invocada.</span><span class="sxs-lookup"><span data-stu-id="91fbb-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="91fbb-270">O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no objeto `_userManager`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="91fbb-271">`_userManager` é fornecida pela injeção de dependência):</span><span class="sxs-lookup"><span data-stu-id="91fbb-271">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="91fbb-272">Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-272">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="91fbb-273">**Observação:** consulte a [confirmação de conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para verificar as etapas para impedir o logon imediato no registro.</span><span class="sxs-lookup"><span data-stu-id="91fbb-273">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="91fbb-274">Fazer Logon</span><span class="sxs-lookup"><span data-stu-id="91fbb-274">Log in</span></span>

<span data-ttu-id="91fbb-275">O formulário de logon é exibido quando:</span><span class="sxs-lookup"><span data-stu-id="91fbb-275">The Login form is displayed when:</span></span>

* <span data-ttu-id="91fbb-276">O link **logon** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="91fbb-276">The **Log in** link is selected.</span></span>
* <span data-ttu-id="91fbb-277">Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.</span><span class="sxs-lookup"><span data-stu-id="91fbb-277">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="91fbb-278">Quando o formulário na página de logon é enviado, a ação de `OnPostAsync` é chamada.</span><span class="sxs-lookup"><span data-stu-id="91fbb-278">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="91fbb-279">`PasswordSignInAsync` é chamado no objeto `_signInManager` (fornecido pela injeção de dependência).</span><span class="sxs-lookup"><span data-stu-id="91fbb-279">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="91fbb-280">A classe base `Controller` expõe uma propriedade `User` que você pode acessar por meio de métodos do controlador.</span><span class="sxs-lookup"><span data-stu-id="91fbb-280">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="91fbb-281">Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização.</span><span class="sxs-lookup"><span data-stu-id="91fbb-281">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="91fbb-282">Para obter mais informações, consulte <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="91fbb-282">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="91fbb-283">Logoff</span><span class="sxs-lookup"><span data-stu-id="91fbb-283">Log out</span></span>

<span data-ttu-id="91fbb-284">O link **logout** invoca a ação `LogoutModel.OnPost`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-284">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="91fbb-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.</span><span class="sxs-lookup"><span data-stu-id="91fbb-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="91fbb-286">Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="91fbb-286">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="91fbb-287">Identidade de teste</span><span class="sxs-lookup"><span data-stu-id="91fbb-287">Test Identity</span></span>

<span data-ttu-id="91fbb-288">Os modelos de projeto Web padrão permitem acesso anônimo às home pages.</span><span class="sxs-lookup"><span data-stu-id="91fbb-288">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="91fbb-289">Para testar a identidade, adicione [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) à página de privacidade.</span><span class="sxs-lookup"><span data-stu-id="91fbb-289">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="91fbb-290">Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** .</span><span class="sxs-lookup"><span data-stu-id="91fbb-290">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="91fbb-291">Você é redirecionado à página de logon.</span><span class="sxs-lookup"><span data-stu-id="91fbb-291">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="91fbb-292">Explorar identidade</span><span class="sxs-lookup"><span data-stu-id="91fbb-292">Explore Identity</span></span>

<span data-ttu-id="91fbb-293">Para explorar a identidade com mais detalhes:</span><span class="sxs-lookup"><span data-stu-id="91fbb-293">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="91fbb-294">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="91fbb-294">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="91fbb-295">Examine a origem de cada página e percorra o depurador.</span><span class="sxs-lookup"><span data-stu-id="91fbb-295">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="91fbb-296">Componentes de identidade</span><span class="sxs-lookup"><span data-stu-id="91fbb-296">Identity Components</span></span>

<span data-ttu-id="91fbb-297">Todos os pacotes NuGet dependentes de identidade estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="91fbb-297">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="91fbb-298">O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="91fbb-298">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="91fbb-299">Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="91fbb-299">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="91fbb-300">Migrando para a identidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91fbb-300">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="91fbb-301">Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="91fbb-301">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="91fbb-302">Definindo a força da senha</span><span class="sxs-lookup"><span data-stu-id="91fbb-302">Setting password strength</span></span>

<span data-ttu-id="91fbb-303">Consulte [configuração](#pw) para obter um exemplo que defina os requisitos mínimos de senha.</span><span class="sxs-lookup"><span data-stu-id="91fbb-303">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="91fbb-304">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="91fbb-304">Next Steps</span></span>

* <span data-ttu-id="91fbb-305">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/5131) para obter informações sobre como configurar a identidade usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="91fbb-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="91fbb-306">Configurar o Identity</span><span class="sxs-lookup"><span data-stu-id="91fbb-306">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
