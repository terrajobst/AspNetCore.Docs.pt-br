---
title: Introdução à identidade do ASP.NET Core
author: rick-anderson
description: Use a identidade com um aplicativo ASP.NET Core. Saiba como definir os requisitos de senha (RequireDigit, RequiredLength, RequiredUniqueChars e mais).
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 325a61e6038e79b9a0db72c8360a5cbff2c8ddae
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011200"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Introdução à identidade do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core identidade é um sistema de associação que adiciona a funcionalidade de logon a aplicativos ASP.NET Core. Os usuários podem criar uma conta com as informações de logon armazenadas em identidade ou podem usar um provedor de logon externo. Os provedores de logon externo com suporte incluem [o Facebook, o Google, a conta da Microsoft e o Twitter](xref:security/authentication/social/index).

A identidade pode ser configurada usando um banco de dados de SQL Server para armazenar nomes de usuário, senhas e de perfil. Como alternativa, outro armazenamento persistente pode ser usado, por exemplo, o armazenamento de tabelas do Azure.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([como baixar)](xref:index#how-to-download-a-sample)).

Neste tópico, você aprenderá a usar a identidade para registrar, fazer logon e fazer logoff de um usuário. Para obter instruções mais detalhadas sobre como criar aplicativos que usam identidade, consulte a seção próximas etapas no final deste artigo.

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity e addidentity

O [AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) foi introduzido no ASP.NET Core 2,1. Chamar `AddDefaultIdentity` é semelhante a chamar o seguinte:

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

Consulte [AddDefaultIdentity Source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) para obter mais informações.

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>Criar um aplicativo Web com autenticação

Crie um projeto de aplicativo Web ASP.NET Core com contas de usuário individuais.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Selecione **Arquivo** > **Novo** > **Projeto**.
* Selecione **Aplicativo Web ASP.NET Core**. Nomeie o projeto **WebApp1** para ter o mesmo namespace do download do projeto. Clique em **OK**.
* Selecione um **aplicativo Web**ASP.NET Core e, em seguida, selecione **alterar autenticação**.
* Selecione **contas de usuário individuais** e clique em **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

O projeto gerado fornece [ASP.NET Core identidade](xref:security/authentication/identity) como uma [biblioteca de classes Razor](xref:razor-pages/ui-class). A biblioteca de classes de identidade Razor expõe pontos de extremidade `Identity` com a área. Por exemplo:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Aplicar migrações

Aplique as migrações para inicializar o banco de dados.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Execute o seguinte comando no console do Gerenciador de pacotes (PMC):

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```cli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Teste de registro e logon

Execute o aplicativo e registre um usuário. Dependendo do tamanho da tela, talvez seja necessário selecionar o botão de alternância de navegação para ver os links de **registro** e **logon** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Configurar serviços de identidade

Os serviços são adicionados `ConfigureServices`no. O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

O código anterior configura a identidade com valores de opção padrão. Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

   A identidade é habilitada chamando [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   Os serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

   A identidade é habilitada para o aplicativo `UseAuthentication` chamando `Configure` no método. `UseAuthentication`Adiciona o [middleware](xref:fundamentals/middleware/index) de autenticação ao pipeline de solicitação.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   Esses serviços são disponibilizados para o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).

   A identidade é habilitada para o aplicativo `UseIdentity` chamando `Configure` no método. `UseIdentity`Adiciona [middleware](xref:fundamentals/middleware/index) de autenticação baseada em cookie ao pipeline de solicitação.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

Para obter mais informações, consulte a [classe identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) e a [inicialização do aplicativo](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Registro em Scaffold, logon e logoff

Siga a [identidade do Scaffold em um projeto do Razor com](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instruções de autorização para gerar o código mostrado nesta seção.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Adicione os arquivos de registro, logon e LogOut.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Se você criou o projeto com o nome **WebApp1**, execute os comandos a seguir. Caso contrário, use o namespace correto para `ApplicationDbContext`o:

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

O PowerShell usa ponto e vírgula como um separador de comando. Ao usar o PowerShell, escape os pontos-e-vírgulas na lista de arquivos ou coloque a lista de arquivos entre aspas duplas, como mostra o exemplo anterior.

---

### <a name="examine-register"></a>Examinar registro

::: moniker range=">= aspnetcore-2.1"

   Quando um usuário clica no link **registrar** , a `RegisterModel.OnPostAsync` ação é invocada. O usuário é criado por [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) no `_userManager` objeto. `_userManager`é fornecido pela injeção de dependência):

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Quando um usuário clica no link **registrar** , a `Register` ação é invocada `AccountController`em. A `Register` ação cria o usuário chamando `CreateAsync` no `_userManager` objeto (fornecido para `AccountController` por injeção de dependência):

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   Se o usuário tiver sido criado com êxito, o usuário será conectado pela chamada para `_signInManager.SignInAsync`.

   **Observação:** Consulte [confirmação da conta](xref:security/authentication/accconfirm#prevent-login-at-registration) para obter as etapas para impedir o logon imediato no registro.

### <a name="log-in"></a>Fazer Logon

::: moniker range=">= aspnetcore-2.1"

O formulário de logon é exibido quando:

* O link **logon** está selecionado.
* Um usuário tenta acessar uma página restrita que não tem autorização para acessar **ou** quando elas não foram autenticadas pelo sistema.

Quando o formulário na página de logon é enviado, a `OnPostAsync` ação é chamada. `PasswordSignInAsync`é chamado no `_signInManager` objeto (fornecido pela injeção de dependência).

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   A classe `Controller` base expõe uma `User` propriedade que você pode acessar por meio de métodos do controlador. Por exemplo, você pode enumerar `User.Claims` e tomar decisões de autorização. Para obter mais informações, consulte <xref:security/authorization/introduction>.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

O formulário de logon é exibido quando os usuários selecionam o link **de logon** ou são redirecionados ao acessar uma página que requer autenticação. Quando o usuário envia o formulário na página de login, a ação`AccountController` `Login` é chamada.

O `Login` faz chamadas `PasswordSignInAsync` no objeto `_signInManager` (fornecido pelo `AccountController` por injeção de dependência).

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

A classe base`Controller` ( `PageModel`ou) expõe uma `User` propriedade. Por exemplo, `User.Claims` pode ser enumerado para tomar decisões de autorização.

::: moniker-end

### <a name="log-out"></a>Fazer logoff

::: moniker range=">= aspnetcore-2.1"

O link **fazer logoff** invoca a `LogoutModel.OnPost` ação. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) limpa as declarações do usuário armazenadas em um cookie.

Post é especificado nas *páginas/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   Clicar no link **fazer logoff** chama a `LogOut` ação.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   O código anterior chama o `_signInManager.SignOutAsync` método. O `SignOutAsync` método limpa as declarações do usuário armazenadas em um cookie.

::: moniker-end

## <a name="test-identity"></a>Identidade de teste

Os modelos de projeto Web padrão permitem acesso anônimo às home pages. Para testar a identidade, [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) adicione à página de privacidade.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

Se você estiver conectado, saia. Execute o aplicativo e selecione o link de **privacidade** . Você será redirecionado para a página de logon.

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>Explorar identidade

Para explorar a identidade com mais detalhes:

* [Criar origem da interface do usuário de identidade completa](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Examine a origem de cada página e percorra o depurador.

::: moniker-end

## <a name="identity-components"></a>Componentes de identidade

::: moniker range=">= aspnetcore-2.1"

Todos os pacotes NuGet dependentes de identidade estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

::: moniker-end

O pacote principal para identidade é [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Esse pacote contém o conjunto principal de interfaces para ASP.NET Core identidade e é incluído pelo `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrando para a identidade do ASP.NET Core

Para obter mais informações e orientações sobre como migrar seu repositório de identidades existente, consulte [migrar autenticação e identidade](xref:migration/identity).

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
