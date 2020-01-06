---
title: Autenticação do Facebook, do Google e do provedor externo sem ASP.NET Core identidade
author: rick-anderson
description: Uma explicação de como usar o Facebook, o Google, o Twitter, etc. a autenticação de usuário da conta sem ASP.NET Core identidade.
ms.author: riande
ms.date: 12/10/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 612964ec9ed4975cdc81780dda3bac6cce96037f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359052"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="029d0-103">Usar autenticação de provedor de entrada social sem identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="029d0-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="029d0-104"><xref:security/authentication/social/index> descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="029d0-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="029d0-105">A abordagem descrita nesse tópico inclui ASP.NET Core identidade como um provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="029d0-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="029d0-106">Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="029d0-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="029d0-107">Isso é útil para aplicativos que não exigem todos os recursos de ASP.NET Core identidade, mas ainda exigem integração com um provedor de autenticação externa confiável.</span><span class="sxs-lookup"><span data-stu-id="029d0-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="029d0-108">Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="029d0-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="029d0-109">Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google.</span><span class="sxs-lookup"><span data-stu-id="029d0-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="029d0-110">Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="029d0-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="029d0-111">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="029d0-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="029d0-112">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="029d0-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="029d0-113">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="029d0-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="029d0-114">Outros provedores</span><span class="sxs-lookup"><span data-stu-id="029d0-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="029d0-115">Configuração do</span><span class="sxs-lookup"><span data-stu-id="029d0-115">Configuration</span></span>

<span data-ttu-id="029d0-116">No método `ConfigureServices`, configure os esquemas de autenticação do aplicativo com os métodos <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:</span><span class="sxs-lookup"><span data-stu-id="029d0-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="029d0-117">A chamada para <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> define o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="029d0-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="029d0-118">O `DefaultScheme` é o esquema padrão usado pelos seguintes métodos de extensão de autenticação `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="029d0-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="029d0-119">Definir o `DefaultScheme` do aplicativo como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") configura o aplicativo para usar cookies como o esquema padrão para esses métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="029d0-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="029d0-120">Definir o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> do aplicativo como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="029d0-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="029d0-121">`DefaultChallengeScheme` substitui `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="029d0-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="029d0-122">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.</span><span class="sxs-lookup"><span data-stu-id="029d0-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="029d0-123">Em `Startup.Configure`, chame `UseAuthentication` e `UseAuthorization` entre `UseRouting` e `UseEndpoints`de chamada.</span><span class="sxs-lookup"><span data-stu-id="029d0-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="029d0-124">Isso define a propriedade `HttpContext.User` e executa o middleware de autorização para solicitações:</span><span class="sxs-lookup"><span data-stu-id="029d0-124">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="029d0-125">Para saber mais sobre esquemas de autenticação, consulte [conceitos de autenticação](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="029d0-125">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="029d0-126">Para saber mais sobre a autenticação de cookie, consulte <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="029d0-126">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="029d0-127">Aplicar autorização</span><span class="sxs-lookup"><span data-stu-id="029d0-127">Apply authorization</span></span>

<span data-ttu-id="029d0-128">Teste a configuração de autenticação do aplicativo aplicando o atributo `AuthorizeAttribute` a um controlador, uma ação ou uma página.</span><span class="sxs-lookup"><span data-stu-id="029d0-128">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="029d0-129">O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:</span><span class="sxs-lookup"><span data-stu-id="029d0-129">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="029d0-130">Sair</span><span class="sxs-lookup"><span data-stu-id="029d0-130">Sign out</span></span>

<span data-ttu-id="029d0-131">Para sair do usuário atual e excluir seu cookie, chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="029d0-131">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="029d0-132">O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :</span><span class="sxs-lookup"><span data-stu-id="029d0-132">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="029d0-133">Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="029d0-133">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="029d0-134">O `DefaultScheme` do aplicativo de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.</span><span class="sxs-lookup"><span data-stu-id="029d0-134">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="029d0-135">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="029d0-135">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="029d0-136"><xref:security/authentication/social/index> descreve como permitir que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="029d0-136"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="029d0-137">A abordagem descrita nesse tópico inclui ASP.NET Core identidade como um provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="029d0-137">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="029d0-138">Este exemplo demonstra como usar um provedor de autenticação externo **sem** ASP.NET Core identidade.</span><span class="sxs-lookup"><span data-stu-id="029d0-138">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="029d0-139">Isso é útil para aplicativos que não exigem todos os recursos de ASP.NET Core identidade, mas ainda exigem integração com um provedor de autenticação externa confiável.</span><span class="sxs-lookup"><span data-stu-id="029d0-139">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="029d0-140">Este exemplo usa a [autenticação do Google](xref:security/authentication/google-logins) para autenticar usuários.</span><span class="sxs-lookup"><span data-stu-id="029d0-140">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="029d0-141">Usar a autenticação do Google muda muitas das complexidades do gerenciamento do processo de entrada para o Google.</span><span class="sxs-lookup"><span data-stu-id="029d0-141">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="029d0-142">Para integrar com um provedor de autenticação externa diferente, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="029d0-142">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="029d0-143">Autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="029d0-143">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="029d0-144">Autenticação da Microsoft</span><span class="sxs-lookup"><span data-stu-id="029d0-144">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="029d0-145">Autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="029d0-145">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="029d0-146">Outros provedores</span><span class="sxs-lookup"><span data-stu-id="029d0-146">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="029d0-147">Configuração do</span><span class="sxs-lookup"><span data-stu-id="029d0-147">Configuration</span></span>

<span data-ttu-id="029d0-148">No método `ConfigureServices`, configure os esquemas de autenticação do aplicativo com os métodos `AddAuthentication`, `AddCookie`e `AddGoogle`:</span><span class="sxs-lookup"><span data-stu-id="029d0-148">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="029d0-149">A chamada para [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) define o [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="029d0-149">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="029d0-150">O `DefaultScheme` é o esquema padrão usado pelos seguintes métodos de extensão de autenticação `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="029d0-150">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="029d0-151">Definir o `DefaultScheme` do aplicativo como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") configura o aplicativo para usar cookies como o esquema padrão para esses métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="029d0-151">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="029d0-152">Definir o <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> do aplicativo como [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configura o aplicativo para usar o Google como o esquema padrão para chamadas para `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="029d0-152">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="029d0-153">`DefaultChallengeScheme` substitui `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="029d0-153">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="029d0-154">Consulte <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> para obter propriedades adicionais que substituem `DefaultScheme` quando definido.</span><span class="sxs-lookup"><span data-stu-id="029d0-154">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="029d0-155">No método `Configure`, chame o método `UseAuthentication` para invocar o middleware de autenticação que define a propriedade `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="029d0-155">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="029d0-156">Chame o método `UseAuthentication` antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="029d0-156">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="029d0-157">Para saber mais sobre esquemas de autenticação, consulte [conceitos de autenticação](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="029d0-157">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="029d0-158">Para saber mais sobre a autenticação de cookie, consulte <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="029d0-158">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="029d0-159">Aplicar autorização</span><span class="sxs-lookup"><span data-stu-id="029d0-159">Apply authorization</span></span>

<span data-ttu-id="029d0-160">Teste a configuração de autenticação do aplicativo aplicando o atributo `AuthorizeAttribute` a um controlador, uma ação ou uma página.</span><span class="sxs-lookup"><span data-stu-id="029d0-160">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="029d0-161">O código a seguir limita o acesso à página de *privacidade* aos usuários que foram autenticados:</span><span class="sxs-lookup"><span data-stu-id="029d0-161">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="029d0-162">Sair</span><span class="sxs-lookup"><span data-stu-id="029d0-162">Sign out</span></span>

<span data-ttu-id="029d0-163">Para sair do usuário atual e excluir seu cookie, chame [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="029d0-163">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="029d0-164">O código a seguir adiciona um `Logout` manipulador de página à página de *índice* :</span><span class="sxs-lookup"><span data-stu-id="029d0-164">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="029d0-165">Observe que a chamada para `SignOutAsync` não especifica um esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="029d0-165">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="029d0-166">O `DefaultScheme` do aplicativo de `CookieAuthenticationDefaults.AuthenticationScheme` é usado como um retorno.</span><span class="sxs-lookup"><span data-stu-id="029d0-166">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="029d0-167">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="029d0-167">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
