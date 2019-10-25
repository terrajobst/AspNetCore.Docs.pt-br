---
title: Identidade Scaffold em projetos ASP.NET Core
author: rick-anderson
description: Saiba como Scaffold a identidade em um projeto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ca2046563281efc3c1cd8f4fec73fe4f8d3fbdda
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816118"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="10330-103">Identidade Scaffold em projetos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10330-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="10330-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="10330-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="10330-105">ASP.NET Core 2,1 e posterior fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="10330-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="10330-106">Os aplicativos que incluem identidade podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na biblioteca de classes do Razor de identidade (RCL).</span><span class="sxs-lookup"><span data-stu-id="10330-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="10330-107">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="10330-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="10330-108">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="10330-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="10330-109">Código gerado tem precedência sobre o mesmo código na RCL de Identidade.</span><span class="sxs-lookup"><span data-stu-id="10330-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="10330-110">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).</span><span class="sxs-lookup"><span data-stu-id="10330-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="10330-111">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o pacote de identidade RCL.</span><span class="sxs-lookup"><span data-stu-id="10330-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="10330-112">Você tem a opção de selecionar o código de Identidade a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="10330-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="10330-113">Embora o scaffolder gere a maior parte do código necessário, você precisará atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="10330-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="10330-114">Este documento explica as etapas necessárias para concluir uma atualização de scaffolding de identidade.</span><span class="sxs-lookup"><span data-stu-id="10330-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="10330-115">Quando a identidade scaffolder é executada, um arquivo *ScaffoldingReadme. txt* é criado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="10330-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="10330-116">O arquivo *ScaffoldingReadme. txt* contém instruções gerais sobre o que é necessário para concluir a atualização do Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="10330-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="10330-117">Este documento contém instruções mais completas do que o arquivo *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="10330-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="10330-118">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="10330-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="10330-119">Inspecione as alterações depois de executar a identidade scaffolder.</span><span class="sxs-lookup"><span data-stu-id="10330-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="10330-120">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com identidade.</span><span class="sxs-lookup"><span data-stu-id="10330-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="10330-121">Serviços ou stubs de serviço não são gerados quando a identidade scaffolding.</span><span class="sxs-lookup"><span data-stu-id="10330-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="10330-122">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="10330-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="10330-123">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="10330-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="10330-124">Scaffold identidade em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="10330-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="10330-125">Adicione as seguintes chamadas realçadas à classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="10330-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="10330-126">Scaffold identidade em um projeto Razor sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="10330-126">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="10330-127">A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="10330-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="10330-128">para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="10330-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="10330-129">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="10330-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="10330-130">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="10330-130">Enable authentication</span></span>

<span data-ttu-id="10330-131">No método `Configure` da classe `Startup`, chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="10330-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="10330-132">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="10330-132">Layout changes</span></span>

<span data-ttu-id="10330-133">Opcional: Adicione o logon parcial (`_LoginPartial`) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="10330-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="10330-134">Scaffold identidade em um projeto Razor com autorização</span><span class="sxs-lookup"><span data-stu-id="10330-134">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="10330-135">Algumas opções de identidade são configuradas em *áreas/identidade/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="10330-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="10330-136">para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="10330-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="10330-137">Scaffold identidade em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="10330-137">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="10330-138">Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo *views/Shared/_ layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="10330-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="10330-139">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="10330-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="10330-140">A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="10330-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="10330-141">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="10330-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="10330-142">Chamar [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="10330-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="10330-143">Scaffold identidade em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="10330-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="10330-144">Exclua as *páginas/pasta compartilhada* e os arquivos nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="10330-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="10330-145">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="10330-145">Create full identity UI source</span></span>

<span data-ttu-id="10330-146">Para manter o controle total da interface do usuário de identidade, execute a identidade scaffolder e selecione **substituir todos os arquivos**.</span><span class="sxs-lookup"><span data-stu-id="10330-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="10330-147">O código realçado a seguir mostra as alterações para substituir a interface do usuário de identidade padrão por identidade em um aplicativo Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="10330-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="10330-148">Talvez você queira fazer isso para ter controle total da interface do usuário da identidade.</span><span class="sxs-lookup"><span data-stu-id="10330-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="10330-149">A identidade padrão é substituída no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="10330-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="10330-150">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="10330-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="10330-151">Registre uma implementação de `IEmailSender`, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="10330-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="10330-152">Página desabilitar registro</span><span class="sxs-lookup"><span data-stu-id="10330-152">Disable register page</span></span>

<span data-ttu-id="10330-153">Para desabilitar o registro do usuário:</span><span class="sxs-lookup"><span data-stu-id="10330-153">To disable user registration:</span></span>

* <span data-ttu-id="10330-154">Identidade Scaffold.</span><span class="sxs-lookup"><span data-stu-id="10330-154">Scaffold Identity.</span></span> <span data-ttu-id="10330-155">Inclua conta. Register, Account. login e Account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="10330-155">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="10330-156">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="10330-156">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="10330-157">Atualize *áreas/identidade/páginas/conta/registro. cshtml. cs* para que os usuários não possam se registrar neste ponto de extremidade:</span><span class="sxs-lookup"><span data-stu-id="10330-157">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="10330-158">Atualize *áreas/identidade/páginas/Account/Register. cshtml* para que sejam consistentes com as alterações anteriores:</span><span class="sxs-lookup"><span data-stu-id="10330-158">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="10330-159">Comente ou remova o link de registro de *áreas/identidade/páginas/conta/login. cshtml*</span><span class="sxs-lookup"><span data-stu-id="10330-159">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="10330-160">Atualize a página *áreas/identidade/páginas/conta/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="10330-160">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="10330-161">Remova o código e os links do arquivo cshtml.</span><span class="sxs-lookup"><span data-stu-id="10330-161">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="10330-162">Remova o código de confirmação do `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="10330-162">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="10330-163">Usar outro aplicativo para adicionar usuários</span><span class="sxs-lookup"><span data-stu-id="10330-163">Use another app to add users</span></span>

<span data-ttu-id="10330-164">Forneça um mecanismo para adicionar usuários fora do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="10330-164">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="10330-165">As opções para adicionar usuários incluem:</span><span class="sxs-lookup"><span data-stu-id="10330-165">Options to add users include:</span></span>

* <span data-ttu-id="10330-166">Um aplicativo Web de administrador dedicado.</span><span class="sxs-lookup"><span data-stu-id="10330-166">A dedicated admin web app.</span></span>
* <span data-ttu-id="10330-167">Um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="10330-167">A console app.</span></span>

<span data-ttu-id="10330-168">O código a seguir descreve uma abordagem para adicionar usuários:</span><span class="sxs-lookup"><span data-stu-id="10330-168">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="10330-169">Uma lista de usuários é lida na memória.</span><span class="sxs-lookup"><span data-stu-id="10330-169">A list of users is read into memory.</span></span>
* <span data-ttu-id="10330-170">Uma senha exclusiva forte é gerada para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="10330-170">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="10330-171">O usuário é adicionado ao banco de dados de identidade.</span><span class="sxs-lookup"><span data-stu-id="10330-171">The user is added to the Identity database.</span></span>
* <span data-ttu-id="10330-172">O usuário é notificado e foi instruído a alterar a senha.</span><span class="sxs-lookup"><span data-stu-id="10330-172">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="10330-173">O código a seguir descreve como adicionar um usuário:</span><span class="sxs-lookup"><span data-stu-id="10330-173">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="10330-174">Uma abordagem semelhante pode ser seguida para cenários de produção.</span><span class="sxs-lookup"><span data-stu-id="10330-174">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10330-175">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="10330-175">Additional resources</span></span>

* [<span data-ttu-id="10330-176">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="10330-176">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
