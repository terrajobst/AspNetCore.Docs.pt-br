---
title: Identidade Scaffold em projetos ASP.NET Core
author: rick-anderson
description: Saiba como Scaffold a identidade em um projeto ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 2432d346d9678157848a38fa01d9057cdd7503ff
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75356286"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identidade Scaffold em projetos ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class). Os aplicativos que incluem identidade podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na biblioteca de classes do Razor de identidade (RCL). Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento. Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro. Código gerado tem precedência sobre o mesmo código na RCL de Identidade. Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).

Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o pacote de identidade RCL. Você tem a opção de selecionar o código de Identidade a ser gerado.

Embora o scaffolder gere a maior parte do código necessário, você precisa atualizar seu projeto para concluir o processo. Este documento explica as etapas necessárias para concluir uma atualização de scaffolding de identidade.

É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações. Inspecione as alterações depois de executar a identidade scaffolder.

Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com identidade. Serviços ou stubs de serviço não são gerados quando a identidade scaffolding. Os serviços para habilitar esses recursos devem ser adicionados manualmente. Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).

Este documento contém instruções mais completas do que o arquivo *ScaffoldingReadme. txt* que é gerado ao executar o scaffolder.

## <a name="scaffold-identity-into-an-empty-project"></a>Scaffold identidade em um projeto vazio

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Atualize a classe `Startup` com um código semelhante ao seguinte:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Scaffold identidade em um projeto Razor sem autorização existente

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
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*. para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrações, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Habilitar autenticação

Atualize a classe `Startup` com um código semelhante ao seguinte:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Alterações de layout

Opcional: Adicione o logon parcial (`_LoginPartial`) ao arquivo de layout:

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Scaffold identidade em um projeto Razor com autorização

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
Algumas opções de identidade são configuradas em *áreas/identidade/IdentityHostingStartup. cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Scaffold identidade em um projeto MVC sem autorização existente

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

Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo *views/Shared/_Layout. cshtml* :

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*

A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*. Para obter mais informações, consulte IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Atualize a classe `Startup` com um código semelhante ao seguinte:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Scaffold identidade em um projeto MVC com autorização

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Criar origem da interface do usuário de identidade completa

Para manter o controle total da interface do usuário de identidade, execute a identidade scaffolder e selecione **substituir todos os arquivos**.

O código realçado a seguir mostra as alterações para substituir a interface do usuário de identidade padrão por identidade em um aplicativo Web ASP.NET Core 2,1. Talvez você queira fazer isso para ter controle total da interface do usuário da identidade.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

A identidade padrão é substituída no código a seguir:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre uma implementação de `IEmailSender`, por exemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Página desabilitar registro

Para desabilitar o registro do usuário:

* Identidade Scaffold. Inclua conta. Register, Account. login e Account. RegisterConfirmation. Por exemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Atualize *áreas/identidade/páginas/conta/registro. cshtml. cs* para que os usuários não possam se registrar neste ponto de extremidade:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Atualize *áreas/identidade/páginas/Account/Register. cshtml* para que sejam consistentes com as alterações anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente ou remova o link de registro de *áreas/identidade/páginas/conta/login. cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Atualize a página *áreas/identidade/páginas/conta/RegisterConfirmation* .

  * Remova o código e os links do arquivo cshtml.
  * Remova o código de confirmação do `PageModel`:

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
  
### <a name="use-another-app-to-add-users"></a>Usar outro aplicativo para adicionar usuários

Forneça um mecanismo para adicionar usuários fora do aplicativo Web. As opções para adicionar usuários incluem:

* Um aplicativo Web de administrador dedicado.
* Um aplicativo de console.

O código a seguir descreve uma abordagem para adicionar usuários:

* Uma lista de usuários é lida na memória.
* Uma senha exclusiva forte é gerada para cada usuário.
* O usuário é adicionado ao banco de dados de identidade.
* O usuário é notificado e foi instruído a alterar a senha.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

O código a seguir descreve como adicionar um usuário:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Uma abordagem semelhante pode ser seguida para cenários de produção.

## <a name="additional-resources"></a>Recursos adicionais

* [Alterações no código de autenticação para ASP.NET Core 2,1 e posterior](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 e posterior fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class). Os aplicativos que incluem identidade podem aplicar o scaffolder para adicionar seletivamente o código-fonte contido na biblioteca de classes do Razor de identidade (RCL). Talvez você queira gerar o código-fonte para que você possa modificar o código e alterar o comportamento. Por exemplo, você pode instruir o scaffolder a gerar o código usado no registro. Código gerado tem precedência sobre o mesmo código na RCL de Identidade. Para obter controle total da interface do usuário e não usar o RCL padrão, consulte a seção [criar origem da interface do usuário de identidade completa](#full).

Os aplicativos que **não** incluem autenticação podem aplicar o scaffolder para adicionar o pacote de identidade RCL. Você tem a opção de selecionar o código de Identidade a ser gerado.

Embora o scaffolder gere a maior parte do código necessário, você precisará atualizar seu projeto para concluir o processo. Este documento explica as etapas necessárias para concluir uma atualização de scaffolding de identidade.

Quando a identidade scaffolder é executada, um arquivo *ScaffoldingReadme. txt* é criado no diretório do projeto. O arquivo *ScaffoldingReadme. txt* contém instruções gerais sobre o que é necessário para concluir a atualização do Identity scaffolding. Este documento contém instruções mais completas do que o arquivo *ScaffoldingReadme. txt* .

É recomendável usar um sistema de controle do código-fonte que mostra diferenças de arquivo e permite que você faça o logout das alterações. Inspecione as alterações depois de executar a identidade scaffolder.

> [!NOTE]
> Os serviços são necessários ao usar a [autenticação de dois fatores](xref:security/authentication/identity-enable-qrcodes), [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)e outros recursos de segurança com identidade. Serviços ou stubs de serviço não são gerados quando a identidade scaffolding. Os serviços para habilitar esses recursos devem ser adicionados manualmente. Por exemplo, consulte [exigir confirmação de email](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Scaffold identidade em um projeto vazio

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Adicione as seguintes chamadas realçadas à classe `Startup`:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Scaffold identidade em um projeto Razor sem autorização existente

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

A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*. para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrações, UseAuthentication e layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Habilitar autenticação

No método `Configure` da classe `Startup`, chame [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Alterações de layout

Opcional: Adicione o logon parcial (`_LoginPartial`) ao arquivo de layout:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Scaffold identidade em um projeto Razor com autorização

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
Algumas opções de identidade são configuradas em *áreas/identidade/IdentityHostingStartup. cs*. Para obter mais informações, consulte [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Scaffold identidade em um projeto MVC sem autorização existente

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

Opcional: Adicione o logon Partial (`_LoginPartial`) ao arquivo *views/Shared/_Layout. cshtml* :

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Mova o arquivo *pages/Shared/_LoginPartial. cshtml* para *views/Shared/_LoginPartial. cshtml*

A identidade é configurada em *áreas/identidade/IdentityHostingStartup. cs*. Para obter mais informações, consulte IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Chamar [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) após `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Scaffold identidade em um projeto MVC com autorização

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Exclua as *páginas/pasta compartilhada* e os arquivos nessa pasta.

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Criar origem da interface do usuário de identidade completa

Para manter o controle total da interface do usuário de identidade, execute a identidade scaffolder e selecione **substituir todos os arquivos**.

O código realçado a seguir mostra as alterações para substituir a interface do usuário de identidade padrão por identidade em um aplicativo Web ASP.NET Core 2,1. Talvez você queira fazer isso para ter controle total da interface do usuário da identidade.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

A identidade padrão é substituída no código a seguir:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

O código a seguir define [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)e [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registre uma implementação de `IEmailSender`, por exemplo:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Página desabilitar registro

Para desabilitar o registro do usuário:

* Identidade Scaffold. Inclua conta. Register, Account. login e Account. RegisterConfirmation. Por exemplo:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Atualize *áreas/identidade/páginas/conta/registro. cshtml. cs* para que os usuários não possam se registrar neste ponto de extremidade:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Atualize *áreas/identidade/páginas/Account/Register. cshtml* para que sejam consistentes com as alterações anteriores:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Comente ou remova o link de registro de *áreas/identidade/páginas/conta/login. cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Atualize a página *áreas/identidade/páginas/conta/RegisterConfirmation* .

  * Remova o código e os links do arquivo cshtml.
  * Remova o código de confirmação do `PageModel`:

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
  
### <a name="use-another-app-to-add-users"></a>Usar outro aplicativo para adicionar usuários

Forneça um mecanismo para adicionar usuários fora do aplicativo Web. As opções para adicionar usuários incluem:

* Um aplicativo Web de administrador dedicado.
* Um aplicativo de console.

O código a seguir descreve uma abordagem para adicionar usuários:

* Uma lista de usuários é lida na memória.
* Uma senha exclusiva forte é gerada para cada usuário.
* O usuário é adicionado ao banco de dados de identidade.
* O usuário é notificado e foi instruído a alterar a senha.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

O código a seguir descreve como adicionar um usuário:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Uma abordagem semelhante pode ser seguida para cenários de produção.

## <a name="additional-resources"></a>Recursos adicionais

* [Alterações no código de autenticação para ASP.NET Core 2,1 e posterior](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end