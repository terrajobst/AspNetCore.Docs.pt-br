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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="a66d0-103">Usar a autenticação do provedor de logon social sem o ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="a66d0-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="a66d0-104"><xref:security/authentication/social/index> Descreve como permitir que os usuários façam logon usando o OAuth 2.0 com as credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="a66d0-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="a66d0-105">A abordagem descrita nesse tópico inclui a identidade do ASP.NET Core como um provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="a66d0-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="a66d0-106">Este exemplo demonstra como usar um provedor de autenticação externos **sem** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="a66d0-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="a66d0-107">Isso é útil para aplicativos que não exigem que todos os recursos do ASP.NET Core Identity, mas ainda exigem integração com um provedor de autenticação externa confiável.</span><span class="sxs-lookup"><span data-stu-id="a66d0-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="a66d0-108">Este exemplo usa [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="a66d0-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="a66d0-109">Autenticação usando o Google desloca muitas complexidades de gerenciar o processo de logon ao Google.</span><span class="sxs-lookup"><span data-stu-id="a66d0-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="a66d0-110">Para integrar com um provedor de autenticação externos diferentes, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="a66d0-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="a66d0-111">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="a66d0-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="a66d0-112">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="a66d0-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="a66d0-113">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="a66d0-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="a66d0-114">Outros provedores</span><span class="sxs-lookup"><span data-stu-id="a66d0-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="a66d0-115">Configuração</span><span class="sxs-lookup"><span data-stu-id="a66d0-115">Configuration</span></span>

<span data-ttu-id="a66d0-116">No `ConfigureServices` método, configure os esquemas de autenticação do aplicativo com o `AddAuthentication`, `AddCookie` e `AddGoogle` métodos:</span><span class="sxs-lookup"><span data-stu-id="a66d0-116">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie` and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="a66d0-117">A chamada para [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) define o aplicativo [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span><span class="sxs-lookup"><span data-stu-id="a66d0-117">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="a66d0-118">O `DefaultScheme` é o esquema padrão usado pelos seguintes `HttpContext` métodos de extensão de autenticação:</span><span class="sxs-lookup"><span data-stu-id="a66d0-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="a66d0-119">Configuração do aplicativo `DefaultScheme` à [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configura o aplicativo para usar Cookies como o esquema padrão para esses métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="a66d0-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="a66d0-120">Configuração do aplicativo <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> à [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="a66d0-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="a66d0-121">`DefaultChallengeScheme` substitui `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="a66d0-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="a66d0-122">Ver <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para propriedades adicionais que substituem `DefaultScheme` ao ser definida.</span><span class="sxs-lookup"><span data-stu-id="a66d0-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="a66d0-123">No `Configure` método, a chamada a `UseAuthentication` método para invocar o Middleware de autenticação define o `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="a66d0-123">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="a66d0-124">Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="a66d0-124">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="a66d0-125">Para saber mais sobre esquemas de autenticação e autenticação de cookie, consulte <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="a66d0-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="applying-authorization"></a><span data-ttu-id="a66d0-126">Aplicação de autorização</span><span class="sxs-lookup"><span data-stu-id="a66d0-126">Applying authorization</span></span>

<span data-ttu-id="a66d0-127">Testar a configuração de autenticação do aplicativo, aplicando o `AuthorizeAttribute` de atributo a uma página, controlador ou ação.</span><span class="sxs-lookup"><span data-stu-id="a66d0-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="a66d0-128">O código a seguir limita o acesso para o *privacidade* página aos usuários que tiverem sido autenticados:</span><span class="sxs-lookup"><span data-stu-id="a66d0-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="a66d0-129">Sair</span><span class="sxs-lookup"><span data-stu-id="a66d0-129">Sign out</span></span>

<span data-ttu-id="a66d0-130">Para desconectar o usuário atual e excluir seus cookies, chame [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="a66d0-130">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span></span> <span data-ttu-id="a66d0-131">O código a seguir adiciona uma `Logout` manipulador de página para o *índice* página:</span><span class="sxs-lookup"><span data-stu-id="a66d0-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="a66d0-132">Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="a66d0-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="a66d0-133">O aplicativo `DefaultScheme` de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um fallback.</span><span class="sxs-lookup"><span data-stu-id="a66d0-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a66d0-134">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a66d0-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>
