---
title: Identidade Scaffold em projetos ASP.NET Core
author: rick-anderson
description: Saiba como Scaffold a identidade em um projeto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: f3ae089d344d95ed84c9720ab4ba2c697400901e
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703769"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="5acaf-103">Identidade Scaffold em projetos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5acaf-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="5acaf-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5acaf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5acaf-105">ASP.NET Core 2,1 e posterior fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="5acaf-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5acaf-106">Os aplicativos que incluem identidade podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na biblioteca de classes do Razor de identidade (RCL).</span><span class="sxs-lookup"><span data-stu-id="5acaf-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="5acaf-107">Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento.</span><span class="sxs-lookup"><span data-stu-id="5acaf-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="5acaf-108">Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro.</span><span class="sxs-lookup"><span data-stu-id="5acaf-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="5acaf-109">Código gerado tem precedência sobre o mesmo código na RCL de Identidade.</span><span class="sxs-lookup"><span data-stu-id="5acaf-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="5acaf-110">Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).</span><span class="sxs-lookup"><span data-stu-id="5acaf-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="5acaf-111">Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o pacote de identidade RCL.</span><span class="sxs-lookup"><span data-stu-id="5acaf-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="5acaf-112">Você tem a opção de selecionar o código de Identidade a ser gerado.</span><span class="sxs-lookup"><span data-stu-id="5acaf-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="5acaf-113">Embora o scaffolder gere a maior parte do código necessário, você precisará atualizar seu projeto para concluir o processo.</span><span class="sxs-lookup"><span data-stu-id="5acaf-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="5acaf-114">Este documento explica as etapas necessárias para concluir uma atualização de scaffolding de identidade.</span><span class="sxs-lookup"><span data-stu-id="5acaf-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="5acaf-115">Quando a identidade scaffolder é executada, um arquivo *ScaffoldingReadme. txt* é criado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="5acaf-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="5acaf-116">O arquivo *ScaffoldingReadme. txt* contém instruções gerais sobre o que é necessário para concluir a atualização do Identity scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5acaf-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="5acaf-117">Este documento contém instruções mais completas do que o arquivo *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="5acaf-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="5acaf-118">É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações.</span><span class="sxs-lookup"><span data-stu-id="5acaf-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="5acaf-119">Inspecione as alterações depois de executar a identidade scaffolder.</span><span class="sxs-lookup"><span data-stu-id="5acaf-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="5acaf-120">Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com identidade.</span><span class="sxs-lookup"><span data-stu-id="5acaf-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="5acaf-121">Serviços ou stubs de serviço não são gerados quando a identidade scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5acaf-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="5acaf-122">Os serviços para habilitar esses recursos devem ser adicionados manualmente.</span><span class="sxs-lookup"><span data-stu-id="5acaf-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="5acaf-123">Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="5acaf-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="5acaf-124">Scaffold identidade em um projeto vazio</span><span class="sxs-lookup"><span data-stu-id="5acaf-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="5acaf-125">Adicione as seguintes chamadas realçadas à classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5acaf-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="5acaf-126">Scaffold identidade em um projeto Razor sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="5acaf-126">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="5acaf-127">A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="5acaf-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5acaf-128">para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5acaf-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="5acaf-129">Migrações, UseAuthentication e layout</span><span class="sxs-lookup"><span data-stu-id="5acaf-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="5acaf-130">Habilitar autenticação</span><span class="sxs-lookup"><span data-stu-id="5acaf-130">Enable authentication</span></span>

<span data-ttu-id="5acaf-131">No método `Configure` da classe `Startup`, chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="5acaf-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="5acaf-132">Alterações de layout</span><span class="sxs-lookup"><span data-stu-id="5acaf-132">Layout changes</span></span>

<span data-ttu-id="5acaf-133">Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo de layout:</span><span class="sxs-lookup"><span data-stu-id="5acaf-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="5acaf-134">Scaffold identidade em um projeto Razor com autorização</span><span class="sxs-lookup"><span data-stu-id="5acaf-134">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="5acaf-135">Algumas opções de identidade são configuradas em *áreas/identidade/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="5acaf-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5acaf-136">Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="5acaf-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="5acaf-137">Scaffold identidade em um projeto MVC sem autorização existente</span><span class="sxs-lookup"><span data-stu-id="5acaf-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="5acaf-138">Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo *views/Shared/_ layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5acaf-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="5acaf-139">Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="5acaf-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="5acaf-140">A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="5acaf-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="5acaf-141">Para obter mais informações, consulte IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="5acaf-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="5acaf-142">Chamar [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="5acaf-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="5acaf-143">Scaffold identidade em um projeto MVC com autorização</span><span class="sxs-lookup"><span data-stu-id="5acaf-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="5acaf-144">Exclua as *páginas/pasta compartilhada* e os arquivos nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="5acaf-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="5acaf-145">Criar origem da interface do usuário de identidade completa</span><span class="sxs-lookup"><span data-stu-id="5acaf-145">Create full identity UI source</span></span>

<span data-ttu-id="5acaf-146">Para manter o controle total da interface do usuário de identidade, execute a identidade scaffolder e selecione **substituir todos os arquivos**.</span><span class="sxs-lookup"><span data-stu-id="5acaf-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="5acaf-147">O código realçado a seguir mostra as alterações para substituir a interface do usuário de identidade padrão por identidade em um aplicativo Web ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="5acaf-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="5acaf-148">Talvez você queira fazer isso para ter controle total da interface do usuário da identidade.</span><span class="sxs-lookup"><span data-stu-id="5acaf-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="5acaf-149">A identidade padrão é substituída no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="5acaf-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="5acaf-150">O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="5acaf-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="5acaf-151">Registre uma implementação `IEmailSender`, por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5acaf-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5acaf-152">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5acaf-152">Additional resources</span></span>

* [<span data-ttu-id="5acaf-153">Alterações no código de autenticação para ASP.NET Core 2,1 e posterior</span><span class="sxs-lookup"><span data-stu-id="5acaf-153">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
