---
title: Autenticação de provedor externo sem o ASP.NET Core Identity, Google e Facebook
author: rick-anderson
description: Uma explicação de como usar o Facebook, Google, Twitter, autenticação de usuário de conta etc. sem o ASP.NET Core Identity.
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 1e7124e8b07c0faf2d005ec3ef55c0414a697d64
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561564"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Usar a autenticação do provedor de logon social sem o ASP.NET Core Identity

<xref:security/authentication/social/index> Descreve como permitir que os usuários façam logon usando o OAuth 2.0 com as credenciais de provedores de autenticação externa. A abordagem descrita nesse tópico inclui a identidade do ASP.NET Core como um provedor de autenticação.

Este exemplo demonstra como usar um provedor de autenticação externos **sem** ASP.NET Core Identity. Isso é útil para aplicativos que não exigem que todos os recursos do ASP.NET Core Identity, mas ainda exigem integração com um provedor de autenticação externa confiável.

Este exemplo usa [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários. Autenticação usando o Google desloca muitas complexidades de gerenciar o processo de logon ao Google. Para integrar com um provedor de autenticação externos diferentes, consulte os tópicos a seguir:

* [Autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Autenticação da Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Outros provedores](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuração

No `ConfigureServices` método, configure os esquemas de autenticação do aplicativo com o `AddAuthentication`, `AddCookie` e `AddGoogle` métodos:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

A chamada para [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) define o aplicativo [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme). O `DefaultScheme` é o esquema padrão usado pelos seguintes `HttpContext` métodos de extensão de autenticação:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Configuração do aplicativo `DefaultScheme` à [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configura o aplicativo para usar Cookies como o esquema padrão para esses métodos de extensão. Configuração do aplicativo <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> à [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`. `DefaultChallengeScheme` substitui `DefaultScheme`. Ver <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para propriedades adicionais que substituem `DefaultScheme` ao ser definida.

No `Configure` método, a chamada a `UseAuthentication` método para invocar o Middleware de autenticação define o `HttpContext.User` propriedade. Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

Para saber mais sobre esquemas de autenticação e autenticação de cookie, consulte <xref:security/authentication/cookie>.

## <a name="applying-authorization"></a>Aplicação de autorização

Testar a configuração de autenticação do aplicativo, aplicando o `AuthorizeAttribute` de atributo a uma página, controlador ou ação. O código a seguir limita o acesso para o *privacidade* página aos usuários que tiverem sido autenticados:

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Sair

Para desconectar o usuário atual e excluir seus cookies, chame [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0). O código a seguir adiciona uma `Logout` manipulador de página para o *índice* página:

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação. O aplicativo `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um fallback.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
