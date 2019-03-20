---
title: Introdução à identidade do ASP.NET Core
author: rick-anderson
description: Use identidade com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e muito mais).
ms.author: riande
ms.date: 08/08/2018
uid: security/authentication/identity
ms.openlocfilehash: d57ab5f2fdfabdfe3a987475d2db68b3fc1dec72
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209194"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Introdução à identidade do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core Identity é um sistema de associação que adiciona a funcionalidade de logon para aplicativos ASP.NET Core. Os usuários podem criar uma conta com as informações de logon armazenadas no Identity ou eles podem usar um provedor de logon externo. Provedores de logon externo com suporte incluem [Facebook, Google, Account da Microsoft e Twitter](xref:security/authentication/social/index).

Identidade pode ser configurada usando um banco de dados do SQL Server para armazenar nomes de usuário, senhas e dados de perfil. Como alternativa, outro repositório persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).

Neste tópico, saiba como usar a identidade para registrar, faça logon e logoff de um usuário. Para obter instruções mais detalhadas sobre como criar aplicativos que usam a identidade, consulte a seção próximas etapas no final deste artigo.

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>
## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity e AddIdentity

[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) foi introduzido no ASP.NET Core 2.1. Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

Ver [AddDefaultIdentity fonte](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>Criar um aplicativo Web com autenticação

Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selecione **Arquivo** > **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**. Nomeie o projeto **WebApp1** para ter o mesmo namespace que o download do projeto. Clique em **OK**.
* Selecione um ASP.NET Core **aplicativo Web**, em seguida, selecione **alterar autenticação**.
* Selecione **contas de usuário individuais** e clique em **Okey**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

Fornece o projeto gerado [ASP.NET Core Identity](xref:security/authentication/identity) como um [biblioteca de classes Razor](xref:razor-pages/ui-class). A biblioteca de classes Razor identidade expõe pontos de extremidade com o `Identity` área. Por exemplo:

* Identidade/logon/conta
* Identidade/logoff/conta
* / Identidade/conta/gerenciar

### <a name="test-register-and-login"></a>Registro de teste e de logon

Execute o aplicativo e registrar um usuário. Dependendo do tamanho da tela, você talvez precise selecionar o botão de alternância de navegação para ver os **registre** e **Login** links.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>
### <a name="configure-identity-services"></a>Configurar os serviços de identidade

Serviços são adicionados no `ConfigureServices`. O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

O código anterior configura a identidade com os valores de opção padrão. Serviços são disponibilizados para o aplicativo por meio [injeção de dependência](xref:fundamentals/dependency-injection).

   Identidade é habilitada por meio da chamada [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` Adiciona a autenticação [middleware](xref:fundamentals/middleware/index) ao pipeline de solicitação.

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   Serviços são disponibilizados para o aplicativo por meio [injeção de dependência](xref:fundamentals/dependency-injection).

   Identidade é habilitada para o aplicativo, chamando `UseAuthentication` no `Configure` método. `UseAuthentication` Adiciona a autenticação [middleware](xref:fundamentals/middleware/index) ao pipeline de solicitação.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   Esses serviços são disponibilizados para o aplicativo por meio [injeção de dependência](xref:fundamentals/dependency-injection).

   Identidade é habilitada para o aplicativo, chamando `UseIdentity` no `Configure` método. `UseIdentity` Adiciona a autenticação baseada em cookie [middleware](xref:fundamentals/middleware/index) ao pipeline de solicitação.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

Para obter mais informações, consulte o [classe IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e [inicialização do aplicativo](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registre-se de Scaffold, logon e logoff

Siga as [criar o scaffolding de identidade em um projeto do Razor com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções para gerar o código mostrado nesta seção.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Adicione os arquivos de registro, logon e logoff.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você criou o projeto com o nome **WebApp1**, execute os seguintes comandos. Caso contrário, use o namespace correto para o `ApplicationDbContext`:

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

O PowerShell usa o ponto e vírgula como separador de comando. Ao usar o PowerShell, a ponto e vírgula na lista de arquivos de escape ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.

---

### <a name="examine-register"></a>Registre-se de examinar

::: moniker range=">= aspnetcore-2.1"

   Quando um usuário clica o **registre** link, o `RegisterModel.OnPostAsync` ação é invocada. O usuário é criado pelo [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) sobre o `_userManager` objeto. `_userManager` é fornecido pela injeção de dependência):

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Quando um usuário clica o **registre** link, o `Register` ação é invocada em `AccountController`. O `Register` ação cria o usuário chamando `CreateAsync` sobre o `_userManager` objeto (fornecidas para `AccountController` pela injeção de dependência):

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   Se o usuário foi criado com êxito, o usuário é conectado pela chamada para `_signInManager.SignInAsync`.

   **Observação:** Ver [confirmação de conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas impedir que o logon imediata no momento do registro.

### <a name="log-in"></a>Fazer Logon

::: moniker range=">= aspnetcore-2.1"

O formulário de logon é exibido quando:

* O **faça logon no** link é selecionado.
* Um usuário tenta acessar uma página restrita que eles não estão autorizados a acessar **ou** quando eles ainda não foram autenticados pelo sistema.

Quando o formulário na página de logon é enviado, o `OnPostAsync` ação é chamada. `PasswordSignInAsync` é chamado de `_signInManager` (fornecido pela injeção de dependência) do objeto.

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   A base `Controller` classe expõe um `User` propriedade que você pode acessar métodos do controlador. Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização. Para obter mais informações, consulte <xref:security/authorization/introduction>.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

O formulário de logon é exibido quando os usuários selecionam o **faça logon no** vincular ou será redirecionado ao acessar uma página que requer autenticação. Quando o usuário envia o formulário na página de login, a ação`AccountController` `Login` é chamada.

O `Login` faz chamadas `PasswordSignInAsync` no objeto `_signInManager` (fornecido pelo `AccountController` por injeção de dependência).

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

A base de (`Controller` ou `PageModel`) classe expõe um `User` propriedade. Por exemplo, `User.Claims` podem ser enumeradas para tomar decisões de autorização.

::: moniker-end

### <a name="log-out"></a>Fazer logoff

::: moniker range=">= aspnetcore-2.1"

O **fazer logoff** link invoca o `LogoutModel.OnPost` ação. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie. Não redirecionar após chamar `SignOutAsync` ou o usuário irá **não** ser desconectado.

POST é especificado na *Pages/Shared/_LoginPartial.cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Clicar a **fazer logoff** vincular chamadas a `LogOut` ação.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   O código anterior chama o `_signInManager.SignOutAsync` método. O `SignOutAsync` método limpa as declarações do usuário armazenadas em um cookie.

::: moniker-end

## <a name="test-identity"></a>Identidade de teste

Os modelos de projeto da web padrão permitem acesso anônimo para as home pages. Para testar a identidade, adicione [ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) para a página de privacidade.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

Se você estiver entrado, saia do serviço. Execute o aplicativo e selecione o **privacidade** link. Você será redirecionado à página de logon.

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Explore a identidade

Para explorar a identidade em mais detalhes:

* [Criar fonte de interface do usuário de identidade completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine a origem de cada página e percorrer o depurador.

::: moniker-end

## <a name="identity-components"></a>Componentes de identidade

::: moniker range=">= aspnetcore-2.1"

Todos os identidade NuGet pacotes dependentes são incluídos na [metapacote Microsoft](xref:fundamentals/metapackage-app).

::: moniker-end

O pacote principal para a identidade está [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Este pacote contém o conjunto principal de interfaces para ASP.NET Core Identity e é incluído por `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrando para o ASP.NET Core Identity

Para obter mais informações e diretrizes sobre como migrar o armazenamento de identidade existente, consulte [migrar autenticação e identidade](xref:migration/identity).

## <a name="setting-password-strength"></a>Definindo a força da senha

Consulte [configuração](#pw) para obter um exemplo que defina os requisitos mínimos de senha.

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
