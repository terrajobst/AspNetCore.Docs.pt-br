---
title: Identidade de Scaffold em projetos ASP.NET Core
author: rick-anderson
description: Saiba como criar o scaffolding de identidade em um projeto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 9e784864ee78bdfb76dab7748f3fb81728b9c289
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622763"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identidade de Scaffold em projetos ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core 2.1 e posterior fornece [ASP.NET Core Identity](xref:security/authentication/identity) como um [biblioteca de classes Razor](xref:razor-pages/ui-class). Aplicativos que incluem a identidade podem aplicar o scaffolder para seletivamente, adicione o código-fonte contido na identidade Razor classe RCL (biblioteca). Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento. Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro. Código gerado tem precedência sobre o mesmo código na RCL de Identidade. Para obter controle total da interface do usuário e usar a RCL padrão, consulte a seção [criar fonte de interface do usuário de identidade completa](#full).

Aplicativos que fazem **não** incluem autenticação pode aplicar o scaffolder para adicionar o pacote de identidade da RCL. Você tem a opção de selecionar o código de Identidade a ser gerado.

Embora o scaffolder gera a maioria do código necessário, você precisará atualizar seu projeto para concluir o processo. Este documento explica as etapas necessárias para concluir uma atualização de scaffolding de identidade.

Quando o scaffolder de identidade é executado, uma *Scaffoldingreadme* arquivo é criado no diretório do projeto. O *Scaffoldingreadme* arquivo contém instruções gerais sobre o que é necessário para concluir a atualização de scaffolding de identidade. Este documento contém instruções mais completas que o *Scaffoldingreadme* arquivo.

É recomendável usar um sistema de controle do código-fonte que mostra as diferenças de arquivo e permite que você faça alterações fora. Inspecione as alterações depois de executar o scaffolder de identidade.

> [!NOTE]
> Os serviços são necessários ao usar [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [recuperação de confirmação e a senha da conta](xref:security/authentication/accconfirm)e outros recursos de segurança com identidade. Serviços ou stubs de serviço não são gerados quando o scaffolding de identidade. Serviços para habilitar esses recursos devem ser adicionados manualmente. Por exemplo, consulte [exigem Email de confirmação](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Identidade de Scaffold em um projeto vazio

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Adicione as seguintes chamadas destacadas para o `Startup` classe:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identidade de Scaffold em um projeto do Razor sem autorização existente

<!--
set projNam=RPnoAuth
set projType=webapp
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

Identidade é configurada no *Areas/Identity/IdentityHostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>As migrações, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Habilitar a autenticação

No `Configure` método da `Startup` classe, chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) depois `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Alterações de layout

Opcional: Adicione o logon parcial (`_LoginPartial`) para o arquivo de layout:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identidade de Scaffold em um projeto do Razor com autorização

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
Algumas opções de identidade são configuradas no *Areas/Identity/IdentityHostingStartup.cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identidade de Scaffold em um projeto do MVC sem autorização existente

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

Opcional: Adicione o logon parcial (`_LoginPartial`) para o *Views/Shared/_Layout.cshtml* arquivo:

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Mover o *Pages/Shared/_LoginPartial.cshtml* arquivo *Views/Shared/_LoginPartial.cshtml*

Identidade é configurada no *Areas/Identity/IdentityHostingStartup.cs*. Para obter mais informações, consulte IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) depois `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identidade de Scaffold em um projeto do MVC com autorização

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Excluir o *páginas/Shared* pasta e os arquivos nessa pasta.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Criar fonte de interface do usuário de identidade completa

Para manter o controle total da interface do usuário de identidade, execute o scaffolder de identidade e selecione **substituir todos os arquivos**.

O seguinte código realçado mostra as alterações para substituir o padrão de identidade da interface do usuário com identidade em um aplicativo web do ASP.NET Core 2.1. Você talvez queira fazer isso para ter controle total sobre a interface do usuário de identidade.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

O padrão de identidade é substituído no código a seguir:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

O seguinte código define a [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrar um `IEmailSender` implementação, por exemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* [Alterações no código de autenticação para o ASP.NET Core 2.1 e posterior](xref:migration/20_21#changes-to-authentication-code)
