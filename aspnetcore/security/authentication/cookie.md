---
title: Usar autenticação de cookie sem o ASP.NET Core Identity
author: rick-anderson
description: Obter uma explicação de como usar a autenticação de cookie sem o ASP.NET Core Identity
ms.author: riande
ms.date: 10/11/2017
uid: security/authentication/cookie
ms.openlocfilehash: f05e5b83359ec1739115293e092eaed0c811c046
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854374"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="d1ab7-103">Usar autenticação de cookie sem o ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d1ab7-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="d1ab7-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d1ab7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d1ab7-105">Como você viu nos tópicos anteriores de autenticação, [ASP.NET Core Identity](xref:security/authentication/identity) é um provedor de autenticação completa e a versão completa para criar e manter os logons.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-105">As you've seen in the earlier authentication topics, [ASP.NET Core Identity](xref:security/authentication/identity) is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="d1ab7-106">No entanto, você talvez queira usar sua própria lógica de autenticação personalizada com autenticação baseada em cookie às vezes.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-106">However, you may want to use your own custom authentication logic with cookie-based authentication at times.</span></span> <span data-ttu-id="d1ab7-107">Você pode usar a autenticação baseada em cookie como um provedor de autenticação autônomo sem o ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-107">You can use cookie-based authentication as a standalone authentication provider without ASP.NET Core Identity.</span></span>

<span data-ttu-id="d1ab7-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1ab7-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1ab7-109">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="d1ab7-110">Use o nome de usuário de Email "maria.rodriguez@contoso.com" e nenhuma senha para a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-110">Use the Email username "maria.rodriguez@contoso.com" and any password to sign in the user.</span></span> <span data-ttu-id="d1ab7-111">O usuário é autenticado na `AuthenticateUser` método na *Pages/Account/Login.cshtml.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="d1ab7-112">Em um exemplo do mundo real, o usuário deve ser autenticado em relação a um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-112">In a real-world example, the user would be authenticated against a database.</span></span>

<span data-ttu-id="d1ab7-113">Para obter informações sobre a autenticação baseada em cookie Migrando do ASP.NET Core 1.x para 2.0, consulte [migrar autenticação e identidade para o tópico do ASP.NET Core 2.0 (autenticação baseada em Cookie)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-113">For information on migrating cookie-based authentication from ASP.NET Core 1.x to 2.0, see [Migrate Authentication and Identity to ASP.NET Core 2.0 topic (Cookie-based Authentication)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span></span>

<span data-ttu-id="d1ab7-114">Para usar a identidade do ASP.NET Core, consulte o [Introdução à identidade](xref:security/authentication/identity) tópico.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-114">To use ASP.NET Core Identity, see the [Introduction to Identity](xref:security/authentication/identity) topic.</span></span>

## <a name="configuration"></a><span data-ttu-id="d1ab7-115">Configuração</span><span class="sxs-lookup"><span data-stu-id="d1ab7-115">Configuration</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d1ab7-116">Se o aplicativo não usa o [metapacote do Microsoft](xref:fundamentals/metapackage-app), criar uma referência de pacote no arquivo de projeto para o [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) pacote (versão 2.1.0 ou mais tarde).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-116">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package (version 2.1.0 or later).</span></span>

<span data-ttu-id="d1ab7-117">No `ConfigureServices` método, crie o serviço de Middleware de autenticação com o `AddAuthentication` e `AddCookie` métodos:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-117">In the `ConfigureServices` method, create the Authentication Middleware service with the `AddAuthentication` and `AddCookie` methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d1ab7-118">`AuthenticationScheme` passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-118">`AuthenticationScheme` passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="d1ab7-119">`AuthenticationScheme` é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-119">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="d1ab7-120">Definindo o `AuthenticationScheme` para `CookieAuthenticationDefaults.AuthenticationScheme` fornece um valor de "Cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-120">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="d1ab7-121">Você pode fornecer qualquer valor de cadeia de caracteres que diferencia o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-121">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="d1ab7-122">Esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-122">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="d1ab7-123">Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, ele usa [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").</span><span class="sxs-lookup"><span data-stu-id="d1ab7-123">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").</span></span>

<span data-ttu-id="d1ab7-124">No `Configure` método, use o `UseAuthentication` método para invocar o Middleware de autenticação define o `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-124">In the `Configure` method, use the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="d1ab7-125">Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-125">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="d1ab7-126">**Opções de AddCookie**</span><span class="sxs-lookup"><span data-stu-id="d1ab7-126">**AddCookie Options**</span></span>

<span data-ttu-id="d1ab7-127">O [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) classe é usada para configurar as opções de provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-127">The [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="d1ab7-128">Opção</span><span class="sxs-lookup"><span data-stu-id="d1ab7-128">Option</span></span> | <span data-ttu-id="d1ab7-129">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1ab7-129">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="d1ab7-130">AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="d1ab7-130">AccessDeniedPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-131">Fornece o caminho para fornecer a uma 302 não encontrado (redirecionamento de URL) quando disparado por `HttpContext.ForbidAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-131">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ForbidAsync`.</span></span> <span data-ttu-id="d1ab7-132">O valor padrão é `/Account/AccessDenied`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-132">The default value is `/Account/AccessDenied`.</span></span> |
| [<span data-ttu-id="d1ab7-133">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="d1ab7-133">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-134">O emissor a ser usado para o [emissor](/dotnet/api/system.security.claims.claim.issuer) propriedade em quaisquer declarações criadas pelo serviço de autenticação de cookie.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-134">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication service.</span></span> |
| [<span data-ttu-id="d1ab7-135">Cookie.Domain</span><span class="sxs-lookup"><span data-stu-id="d1ab7-135">Cookie.Domain</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-136">O nome de domínio no qual o cookie é atendido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-136">The domain name where the cookie is served.</span></span> <span data-ttu-id="d1ab7-137">Por padrão, isso é o nome do host da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-137">By default, this is the host name of the request.</span></span> <span data-ttu-id="d1ab7-138">O navegador envia apenas o cookie em solicitações para um nome de host correspondente.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-138">The browser only sends the cookie in requests to a matching host name.</span></span> <span data-ttu-id="d1ab7-139">Talvez você queira ajustar isso para ter cookies disponíveis para qualquer host no seu domínio.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-139">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="d1ab7-140">Por exemplo, definir o domínio do cookie `.contoso.com` disponibiliza para `contoso.com`, `www.contoso.com`, e `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-140">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="d1ab7-141">Cookie.HttpOnly</span><span class="sxs-lookup"><span data-stu-id="d1ab7-141">Cookie.HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-142">Um sinalizador que indica se o cookie deve ser acessível somente aos servidores.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-142">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="d1ab7-143">A alteração desse valor para `false` permite que os scripts do lado do cliente para acessar o cookie e pode abrir seu aplicativo ao roubo de cookie deve ter de seu aplicativo uma [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerabilidade.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-143">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="d1ab7-144">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-144">The default value is `true`.</span></span> |
| [<span data-ttu-id="d1ab7-145">Cookie.Name</span><span class="sxs-lookup"><span data-stu-id="d1ab7-145">Cookie.Name</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-146">Define o nome do cookie.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-146">Sets the name of the cookie.</span></span> |
| [<span data-ttu-id="d1ab7-147">Cookie.Path</span><span class="sxs-lookup"><span data-stu-id="d1ab7-147">Cookie.Path</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-148">Usado para isolar aplicativos em execução no mesmo nome de host.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-148">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="d1ab7-149">Se você tiver um aplicativo em execução no `/app1` e para restringir os cookies para o aplicativo, defina a `CookiePath` propriedade `/app1`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-149">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="d1ab7-150">Ao fazer isso, o cookie só está disponível em solicitações para `/app1` e qualquer aplicativo abaixo dela.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-150">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="d1ab7-151">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="d1ab7-151">Cookie.SameSite</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-152">Indica se o navegador deve permitir que o cookie a ser anexado a somente solicitações do mesmo site (`SameSiteMode.Strict`) ou solicitações entre sites usando métodos seguros de HTTP e as solicitações do mesmo site (`SameSiteMode.Lax`).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-152">Indicates whether the browser should allow the cookie to be attached to same-site requests only (`SameSiteMode.Strict`) or cross-site requests using safe HTTP methods and same-site requests (`SameSiteMode.Lax`).</span></span> <span data-ttu-id="d1ab7-153">Quando definido como `SameSiteMode.None`, o valor do cabeçalho de cookie não está definido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-153">When set to `SameSiteMode.None`, the cookie header value isn't set.</span></span> <span data-ttu-id="d1ab7-154">Observe que [Middleware do Cookie política](#cookie-policy-middleware) pode substituir o valor fornecido por você.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-154">Note that [Cookie Policy Middleware](#cookie-policy-middleware) might overwrite the value that you provide.</span></span> <span data-ttu-id="d1ab7-155">Para dar suporte à autenticação OAuth, o valor padrão é `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-155">To support OAuth authentication, the default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="d1ab7-156">Para obter mais informações, consulte [autenticação OAuth interrompida devido à política de cookies SameSite](https://github.com/aspnet/Security/issues/1231).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-156">For more information, see [OAuth authentication broken due to SameSite cookie policy](https://github.com/aspnet/Security/issues/1231).</span></span> |
| [<span data-ttu-id="d1ab7-157">Cookie.SecurePolicy</span><span class="sxs-lookup"><span data-stu-id="d1ab7-157">Cookie.SecurePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-158">Um sinalizador que indica se o cookie criado deve ser limitado a HTTPS (`CookieSecurePolicy.Always`), HTTP ou HTTPS (`CookieSecurePolicy.None`), ou o mesmo protocolo usado na solicitação (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-158">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="d1ab7-159">O valor padrão é `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-159">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="d1ab7-160">DataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="d1ab7-160">DataProtectionProvider</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-161">Define o `DataProtectionProvider` que é usado para criar o padrão `TicketDataFormat`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-161">Sets the `DataProtectionProvider` that's used to create the default `TicketDataFormat`.</span></span> <span data-ttu-id="d1ab7-162">Se o `TicketDataFormat` propriedade for definida, o `DataProtectionProvider` opção não é usada.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-162">If the `TicketDataFormat` property is set, the `DataProtectionProvider` option isn't used.</span></span> <span data-ttu-id="d1ab7-163">Se não for fornecido, o provedor de proteção de dados do aplicativo padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-163">If not provided, the app's default data protection provider is used.</span></span> |
| [<span data-ttu-id="d1ab7-164">Eventos</span><span class="sxs-lookup"><span data-stu-id="d1ab7-164">Events</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-165">O manipulador chama métodos no provedor que fornecem o controle de aplicativo em determinados pontos de processamento.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-165">The handler calls methods on the provider that give the app control at certain processing points.</span></span> <span data-ttu-id="d1ab7-166">Se `Events` não são fornecidas, uma instância padrão é fornecida que não faz nada quando os métodos são chamados.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-166">If `Events` aren't provided, a default instance is supplied that does nothing when the methods are called.</span></span> |
| [<span data-ttu-id="d1ab7-167">EventsType</span><span class="sxs-lookup"><span data-stu-id="d1ab7-167">EventsType</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-168">Usado como o tipo de serviço para obter o `Events` instância em vez da propriedade.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-168">Used as the service type to get the `Events` instance instead of the property.</span></span> |
| [<span data-ttu-id="d1ab7-169">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="d1ab7-169">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-170">O `TimeSpan` depois que o tíquete de autenticação armazenado dentro do cookie expira.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-170">The `TimeSpan` after which the authentication ticket stored inside the cookie expires.</span></span> <span data-ttu-id="d1ab7-171">`ExpireTimeSpan` é adicionado à hora atual para criar o tempo de expiração para o tíquete.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-171">`ExpireTimeSpan` is added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="d1ab7-172">O `ExpiredTimeSpan` valor sempre entra no AuthTicket criptografado verificado pelo servidor.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-172">The `ExpiredTimeSpan` value always goes into the encrypted AuthTicket verified by the server.</span></span> <span data-ttu-id="d1ab7-173">Isso também pode acontecer na [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) cabeçalho, mas somente se `IsPersistent` está definido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-173">It may also go into the [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) header, but only if `IsPersistent` is set.</span></span> <span data-ttu-id="d1ab7-174">Para definir `IsPersistent` à `true`, configure o [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passado para `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-174">To set `IsPersistent` to `true`, configure the [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passed to `SignInAsync`.</span></span> <span data-ttu-id="d1ab7-175">O valor padrão de `ExpireTimeSpan` é de 14 dias.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-175">The default value of `ExpireTimeSpan` is 14 days.</span></span> |
| [<span data-ttu-id="d1ab7-176">LoginPath</span><span class="sxs-lookup"><span data-stu-id="d1ab7-176">LoginPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-177">Fornece o caminho para fornecer a uma 302 não encontrado (redirecionamento de URL) quando disparado por `HttpContext.ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-177">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ChallengeAsync`.</span></span> <span data-ttu-id="d1ab7-178">A URL atual que gerou o 401 é adicionada para o `LoginPath` como um parâmetro de cadeia de caracteres de consulta nomeado pelo `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-178">The current URL that generated the 401 is added to the `LoginPath` as a query string parameter named by the `ReturnUrlParameter`.</span></span> <span data-ttu-id="d1ab7-179">Uma vez uma solicitação para o `LoginPath` concede uma nova entrada identidade, o `ReturnUrlParameter` valor é usado para redirecionar o navegador para a URL que causou o código de status não autorizado original.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-179">Once a request to the `LoginPath` grants a new sign-in identity, the `ReturnUrlParameter` value is used to redirect the browser back to the URL that caused the original unauthorized status code.</span></span> <span data-ttu-id="d1ab7-180">O valor padrão é `/Account/Login`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-180">The default value is `/Account/Login`.</span></span> |
| [<span data-ttu-id="d1ab7-181">LogoutPath</span><span class="sxs-lookup"><span data-stu-id="d1ab7-181">LogoutPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-182">Se o `LogoutPath` é fornecido para o manipulador, em seguida, redireciona uma solicitação para o caminho com base no valor da `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-182">If the `LogoutPath` is provided to the handler, then a request to that path redirects based on the value of the `ReturnUrlParameter`.</span></span> <span data-ttu-id="d1ab7-183">O valor padrão é `/Account/Logout`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-183">The default value is `/Account/Logout`.</span></span> |
| [<span data-ttu-id="d1ab7-184">ReturnUrlParameter</span><span class="sxs-lookup"><span data-stu-id="d1ab7-184">ReturnUrlParameter</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-185">Determina o nome do parâmetro de cadeia de consulta que é acrescentado pelo manipulador para uma resposta 302 do encontrado (redirecionamento de URL).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-185">Determines the name of the query string parameter that's appended by the handler for a 302 Found (URL redirect) response.</span></span> <span data-ttu-id="d1ab7-186">`ReturnUrlParameter` é usado quando uma solicitação chega na `LoginPath` ou `LogoutPath` para retornar o navegador a URL original depois que a ação de logon ou logoff é executada.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-186">`ReturnUrlParameter` is used when a request arrives on the `LoginPath` or `LogoutPath` to return the browser to the original URL after the login or logout action is performed.</span></span> <span data-ttu-id="d1ab7-187">O valor padrão é `ReturnUrl`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-187">The default value is `ReturnUrl`.</span></span> |
| [<span data-ttu-id="d1ab7-188">SessionStore</span><span class="sxs-lookup"><span data-stu-id="d1ab7-188">SessionStore</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-189">Um contêiner opcional usado para armazenar a identidade entre solicitações.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-189">An optional container used to store identity across requests.</span></span> <span data-ttu-id="d1ab7-190">Quando usado, somente um identificador de sessão é enviado ao cliente.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-190">When used, only a session identifier is sent to the client.</span></span> <span data-ttu-id="d1ab7-191">`SessionStore` pode ser usado para atenuar problemas potenciais com identidades grandes.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-191">`SessionStore` can be used to mitigate potential problems with large identities.</span></span> |
| [<span data-ttu-id="d1ab7-192">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="d1ab7-192">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-193">Um sinalizador que indica se um novo cookie com um tempo de expiração atualizados deve ser emitido dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-193">A flag indicating if a new cookie with an updated expiration time should be issued dynamically.</span></span> <span data-ttu-id="d1ab7-194">Isso pode acontecer em qualquer solicitação em que o período de expiração do cookie atual mais de 50% expirou.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-194">This can happen on any request where the current cookie expiration period is more than 50% expired.</span></span> <span data-ttu-id="d1ab7-195">A nova data de expiração é movida para frente até ser a data atual mais o `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-195">The new expiration date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="d1ab7-196">Uma [tempo de expiração do cookie absoluto](xref:security/authentication/cookie#absolute-cookie-expiration) pode ser definida usando o `AuthenticationProperties` classe ao chamar `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-196">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="d1ab7-197">Um tempo de expiração absoluto pode melhorar a segurança do seu aplicativo, limitando a quantidade de tempo que o cookie de autenticação é válido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-197">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="d1ab7-198">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-198">The default value is `true`.</span></span> |
| [<span data-ttu-id="d1ab7-199">TicketDataFormat</span><span class="sxs-lookup"><span data-stu-id="d1ab7-199">TicketDataFormat</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-200">O `TicketDataFormat` é usado para proteger e desproteger a identidade e outras propriedades que são armazenadas no valor do cookie.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-200">The `TicketDataFormat` is used to protect and unprotect the identity and other properties that are stored in the cookie value.</span></span> <span data-ttu-id="d1ab7-201">Se não for fornecido, um `TicketDataFormat` é criado usando o [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-201">If not provided, a `TicketDataFormat` is created using the [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span></span> |
| [<span data-ttu-id="d1ab7-202">Validar</span><span class="sxs-lookup"><span data-stu-id="d1ab7-202">Validate</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | <span data-ttu-id="d1ab7-203">Método que verifica que as opções são válidas.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-203">Method that checks that the options are valid.</span></span> |

<span data-ttu-id="d1ab7-204">Definir `CookieAuthenticationOptions` na configuração do serviço para autenticação no `ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-204">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="d1ab7-205">ASP.NET Core 1.x usa cookie [middleware](xref:fundamentals/middleware/index) que serializa uma entidade de usuário em um cookie criptografado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-205">ASP.NET Core 1.x uses cookie [middleware](xref:fundamentals/middleware/index) that serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="d1ab7-206">Em solicitações subsequentes, o cookie é validado, e a entidade de segurança é recriada e atribuída ao `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-206">On subsequent requests, the cookie is validated, and the principal is recreated and assigned to the `HttpContext.User` property.</span></span>

<span data-ttu-id="d1ab7-207">Instalar o [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) pacote do NuGet em seu projeto.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-207">Install the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet package in your project.</span></span> <span data-ttu-id="d1ab7-208">Este pacote contém o cookie de middleware.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-208">This package contains the cookie middleware.</span></span>

<span data-ttu-id="d1ab7-209">Use o `UseCookieAuthentication` método no `Configure` método na sua *Startup.cs* antes do arquivo `UseMvc` ou `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-209">Use the `UseCookieAuthentication` method in the `Configure` method in your *Startup.cs* file before `UseMvc` or `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

<span data-ttu-id="d1ab7-210">**Opções de CookieAuthenticationOptions**</span><span class="sxs-lookup"><span data-stu-id="d1ab7-210">**CookieAuthenticationOptions Options**</span></span>

<span data-ttu-id="d1ab7-211">O [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) classe é usada para configurar as opções de provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-211">The [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="d1ab7-212">Opção</span><span class="sxs-lookup"><span data-stu-id="d1ab7-212">Option</span></span> | <span data-ttu-id="d1ab7-213">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1ab7-213">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="d1ab7-214">AuthenticationScheme</span><span class="sxs-lookup"><span data-stu-id="d1ab7-214">AuthenticationScheme</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-215">Define o esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-215">Sets the authentication scheme.</span></span> <span data-ttu-id="d1ab7-216">`AuthenticationScheme` é útil quando há várias instâncias de autenticação e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-216">`AuthenticationScheme` is useful when there are multiple instances of authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="d1ab7-217">Definindo o `AuthenticationScheme` para `CookieAuthenticationDefaults.AuthenticationScheme` fornece um valor de "Cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-217">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="d1ab7-218">Você pode fornecer qualquer valor de cadeia de caracteres que diferencia o esquema.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-218">You can supply any string value that distinguishes the scheme.</span></span> |
| [<span data-ttu-id="d1ab7-219">AutomaticAuthenticate</span><span class="sxs-lookup"><span data-stu-id="d1ab7-219">AutomaticAuthenticate</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-220">Define um valor para indicar que a autenticação de cookie deve executar em cada solicitação e tente validar e reconstrua qualquer entidade de segurança serializada criado por ele.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-220">Sets a value to indicate that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span> |
| [<span data-ttu-id="d1ab7-221">AutomaticChallenge</span><span class="sxs-lookup"><span data-stu-id="d1ab7-221">AutomaticChallenge</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-222">Se for true, o middleware de autenticação lida com desafios automática.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-222">If true, the authentication middleware handles automatic challenges.</span></span> <span data-ttu-id="d1ab7-223">Se false, o middleware de autenticação altera somente as respostas quando explicitamente indicado pelo `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-223">If false, the authentication middleware only alters responses when explicitly indicated by the `AuthenticationScheme`.</span></span> |
| [<span data-ttu-id="d1ab7-224">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="d1ab7-224">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-225">O emissor a ser usado para o [emissor](/dotnet/api/system.security.claims.claim.issuer) propriedade em quaisquer declarações criado pelo middleware de autenticação de cookie.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-225">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication middleware.</span></span> |
| [<span data-ttu-id="d1ab7-226">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="d1ab7-226">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-227">O nome de domínio no qual o cookie é atendido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-227">The domain name where the cookie is served.</span></span> <span data-ttu-id="d1ab7-228">Por padrão, isso é o nome do host da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-228">By default, this is the host name of the request.</span></span> <span data-ttu-id="d1ab7-229">O navegador só serve o cookie para um nome de host correspondente.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-229">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="d1ab7-230">Talvez você queira ajustar isso para ter cookies disponíveis para qualquer host no seu domínio.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-230">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="d1ab7-231">Por exemplo, definir o domínio do cookie `.contoso.com` disponibiliza para `contoso.com`, `www.contoso.com`, e `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-231">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="d1ab7-232">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="d1ab7-232">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-233">Um sinalizador que indica se o cookie deve ser acessível somente aos servidores.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-233">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="d1ab7-234">A alteração desse valor para `false` permite que os scripts do lado do cliente para acessar o cookie e pode abrir seu aplicativo ao roubo de cookie deve ter de seu aplicativo uma [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerabilidade.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-234">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="d1ab7-235">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-235">The default value is `true`.</span></span> |
| [<span data-ttu-id="d1ab7-236">CookiePath</span><span class="sxs-lookup"><span data-stu-id="d1ab7-236">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-237">Usado para isolar aplicativos em execução no mesmo nome de host.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-237">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="d1ab7-238">Se você tiver um aplicativo em execução no `/app1` e para restringir os cookies para o aplicativo, defina a `CookiePath` propriedade `/app1`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-238">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="d1ab7-239">Ao fazer isso, o cookie só está disponível em solicitações para `/app1` e qualquer aplicativo abaixo dela.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-239">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="d1ab7-240">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="d1ab7-240">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-241">Um sinalizador que indica se o cookie criado deve ser limitado a HTTPS (`CookieSecurePolicy.Always`), HTTP ou HTTPS (`CookieSecurePolicy.None`), ou o mesmo protocolo usado na solicitação (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-241">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="d1ab7-242">O valor padrão é `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-242">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="d1ab7-243">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1ab7-243">Description</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-244">Informações adicionais sobre o tipo de autenticação que será disponibilizado para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-244">Additional information about the authentication type which is made available to the app.</span></span> |
| [<span data-ttu-id="d1ab7-245">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="d1ab7-245">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-246">O `TimeSpan` depois que o tíquete de autenticação expira.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-246">The `TimeSpan` after which the authentication ticket expires.</span></span> <span data-ttu-id="d1ab7-247">Ele é adicionado à hora atual para criar o tempo de expiração para o tíquete.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-247">It's added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="d1ab7-248">Para usar `ExpireTimeSpan`, você deve definir `IsPersistent` ao `true` no `AuthenticationProperties` passado para `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-248">To use `ExpireTimeSpan`, you must set `IsPersistent` to `true` in the `AuthenticationProperties` passed to `SignInAsync`.</span></span> <span data-ttu-id="d1ab7-249">O valor padrão é 14 dias.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-249">The default value is 14 days.</span></span> |
| [<span data-ttu-id="d1ab7-250">SlidingExpiration</span><span class="sxs-lookup"><span data-stu-id="d1ab7-250">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | <span data-ttu-id="d1ab7-251">Um sinalizador que indica se a data de expiração do cookie redefine quando houver mais de metade do `ExpireTimeSpan` intervalo tenha decorrido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-251">A flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="d1ab7-252">A nova hora exipiration é movida para frente até ser a data atual mais o `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-252">The new exipiration time is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="d1ab7-253">Uma [tempo de expiração do cookie absoluto](xref:security/authentication/cookie#absolute-cookie-expiration) pode ser definida usando o `AuthenticationProperties` classe ao chamar `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-253">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="d1ab7-254">Um tempo de expiração absoluto pode melhorar a segurança do seu aplicativo, limitando a quantidade de tempo que o cookie de autenticação é válido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-254">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="d1ab7-255">O valor padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-255">The default value is `true`.</span></span> |

<span data-ttu-id="d1ab7-256">Definir `CookieAuthenticationOptions` para o Middleware de autenticação de Cookie no `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-256">Set `CookieAuthenticationOptions` for the Cookie Authentication Middleware in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a><span data-ttu-id="d1ab7-257">Cookie de Middleware de política</span><span class="sxs-lookup"><span data-stu-id="d1ab7-257">Cookie Policy Middleware</span></span>

<span data-ttu-id="d1ab7-258">[Middleware do cookie política](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) habilita recursos de política de cookie em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-258">[Cookie Policy Middleware](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) enables cookie policy capabilities in an app.</span></span> <span data-ttu-id="d1ab7-259">Adicionar o middleware ao pipeline de processamento de aplicativos é a ordem de minúsculas; ela afeta apenas os componentes registrados depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-259">Adding the middleware to the app processing pipeline is order sensitive; it only affects components registered after it in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 <span data-ttu-id="d1ab7-260">O [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) fornecido para o Cookie de Middleware de política permitem controlar características globais do processamento de cookie e gancho em manipuladores de processamento do cookie quando os cookies são acrescentados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-260">The [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) provided to the Cookie Policy Middleware allow you to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

| <span data-ttu-id="d1ab7-261">Propriedade</span><span class="sxs-lookup"><span data-stu-id="d1ab7-261">Property</span></span> | <span data-ttu-id="d1ab7-262">Descrição</span><span class="sxs-lookup"><span data-stu-id="d1ab7-262">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="d1ab7-263">HttpOnly</span><span class="sxs-lookup"><span data-stu-id="d1ab7-263">HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | <span data-ttu-id="d1ab7-264">Afeta se os cookies devem ser HttpOnly, que é um sinalizador que indica se o cookie deve ser acessível somente aos servidores.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-264">Affects whether cookies must be HttpOnly, which is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="d1ab7-265">O valor padrão é `HttpOnlyPolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-265">The default value is `HttpOnlyPolicy.None`.</span></span> |
| [<span data-ttu-id="d1ab7-266">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="d1ab7-266">MinimumSameSitePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | <span data-ttu-id="d1ab7-267">Afeta o atributo de mesmo site do cookie (veja abaixo).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-267">Affects the cookie's same-site attribute (see below).</span></span> <span data-ttu-id="d1ab7-268">O valor padrão é `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-268">The default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="d1ab7-269">Essa opção está disponível para o ASP.NET Core 2.0 +.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-269">This option is available for ASP.NET Core 2.0+.</span></span> |
| [<span data-ttu-id="d1ab7-270">OnAppendCookie</span><span class="sxs-lookup"><span data-stu-id="d1ab7-270">OnAppendCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | <span data-ttu-id="d1ab7-271">Chamado quando um cookie será acrescentado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-271">Called when a cookie is appended.</span></span> |
| [<span data-ttu-id="d1ab7-272">OnDeleteCookie</span><span class="sxs-lookup"><span data-stu-id="d1ab7-272">OnDeleteCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | <span data-ttu-id="d1ab7-273">Chamado quando um cookie é excluído.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-273">Called when a cookie is deleted.</span></span> |
| [<span data-ttu-id="d1ab7-274">Proteger</span><span class="sxs-lookup"><span data-stu-id="d1ab7-274">Secure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | <span data-ttu-id="d1ab7-275">Afeta se os cookies devem ser seguro.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-275">Affects whether cookies must be Secure.</span></span> <span data-ttu-id="d1ab7-276">O valor padrão é `CookieSecurePolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-276">The default value is `CookieSecurePolicy.None`.</span></span> |

<span data-ttu-id="d1ab7-277">**MinimumSameSitePolicy** (ASP.NET Core 2.0 ou posterior somente)</span><span class="sxs-lookup"><span data-stu-id="d1ab7-277">**MinimumSameSitePolicy** (ASP.NET Core 2.0+ only)</span></span>

<span data-ttu-id="d1ab7-278">O padrão `MinimumSameSitePolicy` valor é `SameSiteMode.Lax` para permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-278">The default `MinimumSameSitePolicy` value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="d1ab7-279">Estritamente impor uma política do mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-279">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="d1ab7-280">Embora essa configuração interrompe OAuth2 e outras esquemas de autenticação entre origens, ela eleva o nível de segurança do cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-280">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="d1ab7-281">A configuração de Middleware de política de Cookie para `MinimumSameSitePolicy` pode afetar sua configuração de `Cookie.SameSite` em `CookieAuthenticationOptions` configurações de acordo com a matriz a seguir.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-281">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect your setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="d1ab7-282">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="d1ab7-282">MinimumSameSitePolicy</span></span> | <span data-ttu-id="d1ab7-283">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="d1ab7-283">Cookie.SameSite</span></span> | <span data-ttu-id="d1ab7-284">Configuração de Cookie.SameSite resultante</span><span class="sxs-lookup"><span data-stu-id="d1ab7-284">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="d1ab7-285">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d1ab7-285">SameSiteMode.None</span></span>     | <span data-ttu-id="d1ab7-286">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d1ab7-286">SameSiteMode.None</span></span><br><span data-ttu-id="d1ab7-287">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d1ab7-287">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1ab7-288">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-288">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1ab7-289">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d1ab7-289">SameSiteMode.None</span></span><br><span data-ttu-id="d1ab7-290">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d1ab7-290">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1ab7-291">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-291">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d1ab7-292">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d1ab7-292">SameSiteMode.Lax</span></span>      | <span data-ttu-id="d1ab7-293">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d1ab7-293">SameSiteMode.None</span></span><br><span data-ttu-id="d1ab7-294">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d1ab7-294">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1ab7-295">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-295">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1ab7-296">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d1ab7-296">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1ab7-297">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d1ab7-297">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1ab7-298">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-298">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d1ab7-299">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-299">SameSiteMode.Strict</span></span>   | <span data-ttu-id="d1ab7-300">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d1ab7-300">SameSiteMode.None</span></span><br><span data-ttu-id="d1ab7-301">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d1ab7-301">SameSiteMode.Lax</span></span><br><span data-ttu-id="d1ab7-302">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-302">SameSiteMode.Strict</span></span> | <span data-ttu-id="d1ab7-303">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-303">SameSiteMode.Strict</span></span><br><span data-ttu-id="d1ab7-304">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-304">SameSiteMode.Strict</span></span><br><span data-ttu-id="d1ab7-305">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d1ab7-305">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="d1ab7-306">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="d1ab7-306">Create an authentication cookie</span></span>

<span data-ttu-id="d1ab7-307">Para criar um cookie contendo informações de usuário, você precisa construir uma [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-307">To create a cookie holding user information, you must construct a [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span></span> <span data-ttu-id="d1ab7-308">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-308">The user information is serialized and stored in the cookie.</span></span> 

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d1ab7-309">Criar uma [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) com qualquer necessárias [declaração](/dotnet/api/system.security.claims.claim)s e chame [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) para a entrada do usuário:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-309">Create a [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) with any required [Claim](/dotnet/api/system.security.claims.claim)s and call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="d1ab7-310">Chame [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) para a entrada do usuário:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-310">Call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) to sign in the user:</span></span>

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

<span data-ttu-id="d1ab7-311">`SignInAsync` cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-311">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="d1ab7-312">Se você não especificar um `AuthenticationScheme`, o esquema padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-312">If you don't specify an `AuthenticationScheme`, the default scheme is used.</span></span>

<span data-ttu-id="d1ab7-313">Nos bastidores, a criptografia usada é ASP.NET Core [proteção de dados](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) sistema.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-313">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="d1ab7-314">Se você estiver hospedando o aplicativo em várias máquinas, balanceamento de carga entre aplicativos ou usar uma web farm, você deve [configurar a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo anel de chave e o identificador do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-314">If you're hosting app on multiple machines, load balancing across apps, or using a web farm, then you must [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="d1ab7-315">Sair</span><span class="sxs-lookup"><span data-stu-id="d1ab7-315">Sign out</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d1ab7-316">Para desconectar o usuário atual e excluir seus cookies, chame [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span><span class="sxs-lookup"><span data-stu-id="d1ab7-316">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="d1ab7-317">Para desconectar o usuário atual e excluir seus cookies, chame [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span><span class="sxs-lookup"><span data-stu-id="d1ab7-317">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span></span>

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

<span data-ttu-id="d1ab7-318">Se você não estiver usando `CookieAuthenticationDefaults.AuthenticationScheme` (ou "Cookies") como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-318">If you aren't using `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") as the scheme (for example, "ContosoCookie"), supply the scheme you used when configuring the authentication provider.</span></span> <span data-ttu-id="d1ab7-319">Caso contrário, o esquema padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-319">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="d1ab7-320">Reagir às alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="d1ab7-320">React to back-end changes</span></span>

<span data-ttu-id="d1ab7-321">Depois que um cookie é criado, ele se torna a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-321">Once a cookie is created, it becomes the single source of identity.</span></span> <span data-ttu-id="d1ab7-322">Mesmo se você desabilitar um usuário em seus sistemas de back-end, o sistema de autenticação de cookie não tem conhecimento disso, e um usuário permaneça conectado enquanto seu cookie é válido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-322">Even if you disable a user in your back-end systems, the cookie authentication system has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="d1ab7-323">O [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) evento no ASP.NET Core 2.x ou o [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) método no ASP.NET Core 1.x pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-323">The [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) event in ASP.NET Core 2.x or the [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) method in ASP.NET Core 1.x can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="d1ab7-324">Essa abordagem minimiza o risco de usuários revogados acessando o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-324">This approach mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="d1ab7-325">Uma abordagem de validação de cookie baseia-se em manter o controle de quando o banco de dados do usuário foi alterado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-325">One approach to cookie validation is based on keeping track of when the user database has been changed.</span></span> <span data-ttu-id="d1ab7-326">Se o banco de dados não foi alterado desde que o cookie do usuário foi emitido, não é necessário para autenticar o usuário novamente se o cookie ainda é válido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-326">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="d1ab7-327">Para implementar este cenário, o banco de dados, que é implementado no `IUserRepository` para este exemplo armazena um `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-327">To implement this scenario, the database, which is implemented in `IUserRepository` for this example, stores a `LastChanged` value.</span></span> <span data-ttu-id="d1ab7-328">Quando nenhum usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-328">When any user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="d1ab7-329">Para invalidar um cookie, quando as alterações de banco de dados com base nas `LastChanged` o valor, criar o cookie com um `LastChanged` contendo atual de declaração `LastChanged` valor do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-329">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="d1ab7-330">Para implementar uma substituição para o `ValidatePrincipal` eventos, escreva um método com a assinatura a seguir em uma classe que você deriva [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span><span class="sxs-lookup"><span data-stu-id="d1ab7-330">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that you derive from [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="d1ab7-331">Um exemplo é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-331">An example looks like the following:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="d1ab7-332">Registrar a instância de eventos durante o registro do serviço de cookie no `ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-332">Register the events instance during cookie service registration in the `ConfigureServices` method.</span></span> <span data-ttu-id="d1ab7-333">Fornecer um registro de serviço com escopo para sua `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-333">Provide a scoped service registration for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="d1ab7-334">Para implementar uma substituição para o `ValidateAsync` eventos, escreva um método com a seguinte assinatura:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-334">To implement an override for the `ValidateAsync` event, write a method with the following signature:</span></span>

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

<span data-ttu-id="d1ab7-335">Identidade do ASP.NET Core implementa essa verificação como parte de sua [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span><span class="sxs-lookup"><span data-stu-id="d1ab7-335">ASP.NET Core Identity implements this check as part of its [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span></span> <span data-ttu-id="d1ab7-336">Um exemplo é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-336">An example looks like the following:</span></span>

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="d1ab7-337">Registrar o evento durante a configuração de autenticação de cookie no `Configure` método:</span><span class="sxs-lookup"><span data-stu-id="d1ab7-337">Register the event during cookie authentication configuration in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

::: moniker-end

<span data-ttu-id="d1ab7-338">Considere uma situação em que o nome do usuário é atualizado &mdash; uma decisão que não afeta a segurança de qualquer forma.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-338">Consider a situation in which the user's name is updated &mdash; a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="d1ab7-339">Se você quiser atualizar forma não destrutiva a entidade de usuário, chame `context.ReplacePrincipal` e defina o `context.ShouldRenew` propriedade `true`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-339">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d1ab7-340">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-340">The approach described here is triggered on every request.</span></span> <span data-ttu-id="d1ab7-341">Isso pode resultar em uma penalidade de desempenho grande para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-341">This can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="d1ab7-342">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="d1ab7-342">Persistent cookies</span></span>

<span data-ttu-id="d1ab7-343">Talvez você queira que o cookie para persistir entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-343">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="d1ab7-344">Essa persistência deve ser habilitada apenas com o consentimento explícito do usuário com uma caixa de seleção "Lembrar-Me" no logon ou um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-344">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on login or a similar mechanism.</span></span> 

<span data-ttu-id="d1ab7-345">O trecho de código a seguir cria uma identidade e o cookie correspondente que sobrevive a por meio de fechamentos de navegador.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-345">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="d1ab7-346">Quaisquer configurações de expiração deslizante configuradas anteriormente são consideradas.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-346">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="d1ab7-347">Se o cookie expirar enquanto o navegador é fechado, o navegador limpa o cookie depois que ele seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-347">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="d1ab7-348">O [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) classe reside no `Microsoft.AspNetCore.Authentication` namespace.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-348">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) class resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="d1ab7-349">O [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) classe reside no `Microsoft.AspNetCore.Http.Authentication` namespace.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-349">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) class resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

::: moniker-end

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="d1ab7-350">Expiração do cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="d1ab7-350">Absolute cookie expiration</span></span>

<span data-ttu-id="d1ab7-351">Você pode definir um tempo de expiração absoluto com `ExpiresUtc`.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-351">You can set an absolute expiration time with `ExpiresUtc`.</span></span> <span data-ttu-id="d1ab7-352">Você também deve definir `IsPersistent`; caso contrário, `ExpiresUtc` será ignorado e um cookie de sessão único é criado.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-352">You must also set `IsPersistent`; otherwise, `ExpiresUtc` is ignored and a single-session cookie is created.</span></span> <span data-ttu-id="d1ab7-353">Quando `ExpiresUtc` é definida em `SignInAsync`, ele substitui o valor da `ExpireTimeSpan` opção de `CookieAuthenticationOptions`, se definido.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-353">When `ExpiresUtc` is set on `SignInAsync`, it overrides the value of the `ExpireTimeSpan` option of `CookieAuthenticationOptions`, if set.</span></span>

<span data-ttu-id="d1ab7-354">O trecho de código a seguir cria uma identidade e o cookie correspondente que dura por 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-354">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="d1ab7-355">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="d1ab7-355">This ignores any sliding expiration settings previously configured.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d1ab7-356">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d1ab7-356">Additional resources</span></span>

* [<span data-ttu-id="d1ab7-357">As alterações do AUTH 2.0 / migração comunicado</span><span class="sxs-lookup"><span data-stu-id="d1ab7-357">Auth 2.0 Changes / Migration Announcement</span></span>](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="d1ab7-358">Verificações de função baseado em políticas</span><span class="sxs-lookup"><span data-stu-id="d1ab7-358">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
