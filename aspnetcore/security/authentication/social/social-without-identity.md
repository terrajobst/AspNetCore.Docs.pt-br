---
title: Autenticação do Facebook, do Google e do provedor externo sem ASP.NET Core identidade
author: rick-anderson
description: Uma explicação de como usar o Facebook, o Google, o Twitter, etc. a autenticação de usuário da conta sem ASP.NET Core identidade.
ms.author: riande
ms.date: 12/10/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: b30ce7055b35b721c7fb83b61a328200d6a136b1
ms.sourcegitcommit: 3ca4a2235a8129def9e480d0a6ad54cc856920ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025401"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Usar autenticação de provedor de entrada social sem identidade ASP.NET Core

Por [Kirk Larkin](https://twitter.com/serpent5) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa. A abordagem descrita nesse tópico inclui ASP.NET Core identidade como um provedor de autenticação.

Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core identidade. Isso é útil para aplicativos que não exigem todos os recursos de ASP.NET Core identidade, mas ainda exigem integração com um provedor de autenticação externa confiável.

Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários. Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google. Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:

* [Autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Autenticação da Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Outros provedores](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuração

No método `ConfigureServices`, configure os esquemas de autenticação do aplicativo com os métodos <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

A chamada para <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> define o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>do aplicativo. O `DefaultScheme` é o esquema padrão usado pelos seguintes métodos de extensão de autenticação `HttpContext`:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Definir o `DefaultScheme` do aplicativo como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") configura o aplicativo para usar cookies como o esquema padrão para esses métodos de extensão. Definir o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> do aplicativo como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`. `DefaultChallengeScheme` substitui `DefaultScheme`. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.

Em `Startup.Configure`, chame `UseAuthentication` e `UseAuthorization` entre `UseRouting` e `UseEndpoints`de chamada. Isso define a propriedade `HttpContext.User` e executa o middleware de autorização para solicitações:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Para saber mais sobre esquemas de autenticação, consulte [conceitos de autenticação](xref:security/authentication/index#authentication-concepts). Para saber mais sobre a autenticação de cookie, consulte <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Aplicar autorização

Teste a configuração de autenticação do aplicativo aplicando o atributo `AuthorizeAttribute` a um controlador, uma ação ou uma página. O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Sair

Para sair do usuário atual e excluir seu cookie, chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação. O `DefaultScheme` do aplicativo de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa. A abordagem descrita nesse tópico inclui ASP.NET Core identidade como um provedor de autenticação.

Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core identidade. Isso é útil para aplicativos que não exigem todos os recursos de ASP.NET Core identidade, mas ainda exigem integração com um provedor de autenticação externa confiável.

Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários. Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google. Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:

* [Autenticação do Facebook](xref:security/authentication/facebook-logins)
* [Autenticação da Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticação do Twitter](xref:security/authentication/twitter-logins)
* [Outros provedores](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Configuração

No método `ConfigureServices`, configure os esquemas de autenticação do aplicativo com os métodos `AddAuthentication`, `AddCookie`e `AddGoogle`:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

A chamada para [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) define o [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)do aplicativo. O `DefaultScheme` é o esquema padrão usado pelos seguintes métodos de extensão de autenticação `HttpContext`:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Definir o `DefaultScheme` do aplicativo como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") configura o aplicativo para usar cookies como o esquema padrão para esses métodos de extensão. Definir o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> do aplicativo como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`. `DefaultChallengeScheme` substitui `DefaultScheme`. Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.

No método `Configure`, chame o método `UseAuthentication` para invocar o middleware de autenticação que define a propriedade `HttpContext.User`. Chame o método `UseAuthentication` antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Para saber mais sobre esquemas de autenticação, consulte [conceitos de autenticação](xref:security/authentication/index#authentication-concepts). Para saber mais sobre a autenticação de cookie, consulte <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Aplicar autorização

Teste a configuração de autenticação do aplicativo aplicando o atributo `AuthorizeAttribute` a um controlador, uma ação ou uma página. O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Sair

Para sair do usuário atual e excluir seu cookie, chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação. O `DefaultScheme` do aplicativo de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
