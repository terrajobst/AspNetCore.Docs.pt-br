---
title: Autenticação do Facebook, do Google e do provedor externo sem ASP.NET Core identidade
author: rick-anderson
description: Uma explicação de como usar o Facebook, o Google, o Twitter, etc. a autenticação de usuário da conta sem ASP.NET Core identidade.
ms.author: riande
ms.date: 11/19/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 680ea091dcc5ed7f94879b5d277e8be7e5abeb7b
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289112"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="8536d-103">Usar autenticação de provedor de entrada social sem identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8536d-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8536d-104"><xref:security/authentication/social/index> descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="8536d-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="8536d-105">A abordagem descrita nesse tópico inclui ASP.NET Core identidade como um provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="8536d-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="8536d-106">Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="8536d-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="8536d-107">Isso é útil para aplicativos que não exigem todos os recursos de ASP.NET Core identidade, mas ainda exigem integração com um provedor de autenticação externa confiável.</span><span class="sxs-lookup"><span data-stu-id="8536d-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="8536d-108">Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="8536d-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="8536d-109">Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google.</span><span class="sxs-lookup"><span data-stu-id="8536d-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="8536d-110">Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="8536d-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="8536d-111">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="8536d-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="8536d-112">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="8536d-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="8536d-113">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="8536d-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="8536d-114">Outros provedores</span><span class="sxs-lookup"><span data-stu-id="8536d-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="8536d-115">Configuração</span><span class="sxs-lookup"><span data-stu-id="8536d-115">Configuration</span></span>

<span data-ttu-id="8536d-116">No método `ConfigureServices`, configure os esquemas de autenticação do aplicativo com os métodos <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:</span><span class="sxs-lookup"><span data-stu-id="8536d-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="8536d-117">A chamada para <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> define o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8536d-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="8536d-118">O `DefaultScheme` é o esquema padrão usado pelos seguintes métodos de extensão de autenticação `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="8536d-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="8536d-119">Definir o `DefaultScheme` do aplicativo como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") configura o aplicativo para usar cookies como o esquema padrão para esses métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="8536d-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="8536d-120">Definir o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> do aplicativo como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="8536d-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="8536d-121">`DefaultChallengeScheme` substitui `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="8536d-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="8536d-122">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.</span><span class="sxs-lookup"><span data-stu-id="8536d-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="8536d-123">Em `Startup.Configure`, chame `UseAuthentication` e `UseAuthorization` entre `UseRouting` e `UseEndpoints`de chamada.</span><span class="sxs-lookup"><span data-stu-id="8536d-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="8536d-124">Isso define a propriedade `HttpContext.User` e executa o middleware de autorização para solicitações:</span><span class="sxs-lookup"><span data-stu-id="8536d-124">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="8536d-125">Para saber mais sobre os esquemas de autenticação e a autenticação de cookie, consulte <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="8536d-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="8536d-126">Aplicar autorização</span><span class="sxs-lookup"><span data-stu-id="8536d-126">Apply authorization</span></span>

<span data-ttu-id="8536d-127">Teste a configuração de autenticação do aplicativo aplicando o atributo `AuthorizeAttribute` a um controlador, uma ação ou uma página.</span><span class="sxs-lookup"><span data-stu-id="8536d-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="8536d-128">O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:</span><span class="sxs-lookup"><span data-stu-id="8536d-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="8536d-129">Sair</span><span class="sxs-lookup"><span data-stu-id="8536d-129">Sign out</span></span>

<span data-ttu-id="8536d-130">Para sair do usuário atual e excluir seu cookie, chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="8536d-130">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="8536d-131">O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :</span><span class="sxs-lookup"><span data-stu-id="8536d-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="8536d-132">Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="8536d-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="8536d-133">O `DefaultScheme` do aplicativo de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.</span><span class="sxs-lookup"><span data-stu-id="8536d-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8536d-134">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8536d-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8536d-135"><xref:security/authentication/social/index> descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="8536d-135"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="8536d-136">A abordagem descrita nesse tópico inclui ASP.NET Core identidade como um provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="8536d-136">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="8536d-137">Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="8536d-137">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="8536d-138">Isso é útil para aplicativos que não exigem todos os recursos de ASP.NET Core identidade, mas ainda exigem integração com um provedor de autenticação externa confiável.</span><span class="sxs-lookup"><span data-stu-id="8536d-138">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="8536d-139">Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="8536d-139">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="8536d-140">Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google.</span><span class="sxs-lookup"><span data-stu-id="8536d-140">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="8536d-141">Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="8536d-141">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="8536d-142">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="8536d-142">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="8536d-143">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="8536d-143">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="8536d-144">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="8536d-144">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="8536d-145">Outros provedores</span><span class="sxs-lookup"><span data-stu-id="8536d-145">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="8536d-146">Configuração</span><span class="sxs-lookup"><span data-stu-id="8536d-146">Configuration</span></span>

<span data-ttu-id="8536d-147">No método `ConfigureServices`, configure os esquemas de autenticação do aplicativo com os métodos `AddAuthentication`, `AddCookie`e `AddGoogle`:</span><span class="sxs-lookup"><span data-stu-id="8536d-147">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="8536d-148">A chamada para [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) define o [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8536d-148">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="8536d-149">O `DefaultScheme` é o esquema padrão usado pelos seguintes métodos de extensão de autenticação `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="8536d-149">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="8536d-150">Definir o `DefaultScheme` do aplicativo como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") configura o aplicativo para usar cookies como o esquema padrão para esses métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="8536d-150">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="8536d-151">Definir o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> do aplicativo como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="8536d-151">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="8536d-152">`DefaultChallengeScheme` substitui `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="8536d-152">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="8536d-153">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.</span><span class="sxs-lookup"><span data-stu-id="8536d-153">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="8536d-154">No método `Configure`, chame o método `UseAuthentication` para invocar o middleware de autenticação que define a propriedade `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="8536d-154">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="8536d-155">Chame o método `UseAuthentication` antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="8536d-155">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="8536d-156">Para saber mais sobre os esquemas de autenticação e a autenticação de cookie, consulte <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="8536d-156">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="8536d-157">Aplicar autorização</span><span class="sxs-lookup"><span data-stu-id="8536d-157">Apply authorization</span></span>

<span data-ttu-id="8536d-158">Teste a configuração de autenticação do aplicativo aplicando o atributo `AuthorizeAttribute` a um controlador, uma ação ou uma página.</span><span class="sxs-lookup"><span data-stu-id="8536d-158">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="8536d-159">O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:</span><span class="sxs-lookup"><span data-stu-id="8536d-159">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="8536d-160">Sair</span><span class="sxs-lookup"><span data-stu-id="8536d-160">Sign out</span></span>

<span data-ttu-id="8536d-161">Para sair do usuário atual e excluir seu cookie, chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="8536d-161">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="8536d-162">O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :</span><span class="sxs-lookup"><span data-stu-id="8536d-162">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="8536d-163">Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="8536d-163">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="8536d-164">O `DefaultScheme` do aplicativo de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.</span><span class="sxs-lookup"><span data-stu-id="8536d-164">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8536d-165">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8536d-165">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
