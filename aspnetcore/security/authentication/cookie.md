---
title: Usar autenticação de cookie sem identidade ASP.NET Core
author: rick-anderson
description: Saiba como usar a autenticação de cookie sem ASP.NET Core identidade.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: 62a3d247dade6c83156a8378407d5e3891713fd1
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172114"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="85760-103">Usar autenticação de cookie sem identidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85760-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="85760-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="85760-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85760-105">A identidade de ASP.NET Core é um provedor de autenticação completo e repleto de recursos para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="85760-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="85760-106">No entanto, um provedor de autenticação baseado em cookie sem ASP.NET Core identidade pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="85760-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="85760-107">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="85760-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="85760-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85760-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="85760-109">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="85760-110">Use o endereço de **Email** `maria.rodriguez@contoso.com` e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="85760-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="85760-111">O usuário é autenticado no método `AuthenticateUser` no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="85760-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="85760-112">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="85760-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="85760-113">Configuração</span><span class="sxs-lookup"><span data-stu-id="85760-113">Configuration</span></span>

<span data-ttu-id="85760-114">Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="85760-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="85760-115">No método `Startup.ConfigureServices`, crie os serviços de middleware de autenticação com os métodos <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:</span><span class="sxs-lookup"><span data-stu-id="85760-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="85760-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="85760-117">`AuthenticationScheme` é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="85760-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="85760-118">Definir o `AuthenticationScheme` como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="85760-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="85760-119">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="85760-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="85760-120">O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="85760-121">Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, ele usa `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").</span><span class="sxs-lookup"><span data-stu-id="85760-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="85760-122">A propriedade <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> do cookie de autenticação é definida como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="85760-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="85760-123">Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="85760-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="85760-124">Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="85760-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="85760-125">Em `Startup.Configure`, chame `UseAuthentication` e `UseAuthorization` para definir a propriedade `HttpContext.User` e executar o middleware de autorização para solicitações.</span><span class="sxs-lookup"><span data-stu-id="85760-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="85760-126">Chame os métodos `UseAuthentication` e `UseAuthorization` antes de chamar `UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="85760-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="85760-127">A classe <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="85760-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="85760-128">Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="85760-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="85760-129">Middleware de política de cookie</span><span class="sxs-lookup"><span data-stu-id="85760-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="85760-130">O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie.</span><span class="sxs-lookup"><span data-stu-id="85760-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="85760-131">Adicionar o middleware ao pipeline de processamento de aplicativo é sensível à ordem&mdash;ele afeta apenas os componentes downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="85760-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="85760-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido ao middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="85760-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="85760-133">O valor de <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> padrão é `SameSiteMode.Lax` para permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="85760-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="85760-134">Para impor estritamente uma política do mesmo site de `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="85760-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="85760-135">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="85760-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="85760-136">A configuração de middleware de política de cookie para `MinimumSameSitePolicy` pode afetar a configuração de `Cookie.SameSite` nas configurações de `CookieAuthenticationOptions` de acordo com a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="85760-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="85760-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="85760-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="85760-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="85760-138">Cookie.SameSite</span></span> | <span data-ttu-id="85760-139">Cookie resultante. configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="85760-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="85760-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-140">SameSiteMode.None</span></span>     | <span data-ttu-id="85760-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-141">SameSiteMode.None</span></span><br><span data-ttu-id="85760-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="85760-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-144">SameSiteMode.None</span></span><br><span data-ttu-id="85760-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="85760-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="85760-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-148">SameSiteMode.None</span></span><br><span data-ttu-id="85760-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="85760-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="85760-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="85760-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-155">SameSiteMode.None</span></span><br><span data-ttu-id="85760-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="85760-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="85760-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="85760-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="85760-161">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="85760-161">Create an authentication cookie</span></span>

<span data-ttu-id="85760-162">Para criar um cookie que contém informações do usuário, construa um <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="85760-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="85760-163">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="85760-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="85760-164">Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer <xref:System.Security.Claims.Claim>s necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="85760-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="85760-165">`SignInAsync` cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="85760-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="85760-166">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="85760-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="85760-167">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="85760-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="85760-168">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="85760-169">Sair</span><span class="sxs-lookup"><span data-stu-id="85760-169">Sign out</span></span>

<span data-ttu-id="85760-170">Para desconectar o usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="85760-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="85760-171">Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="85760-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="85760-172">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="85760-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="85760-173">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="85760-173">React to back-end changes</span></span>

<span data-ttu-id="85760-174">Depois que um cookie é criado, o cookie é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="85760-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="85760-175">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="85760-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="85760-176">O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="85760-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="85760-177">O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.</span><span class="sxs-lookup"><span data-stu-id="85760-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="85760-178">O evento <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="85760-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="85760-179">A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="85760-180">Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="85760-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="85760-181">Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="85760-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="85760-182">No aplicativo de exemplo, o banco de dados é implementado em `IUserRepository` e armazena um valor de `LastChanged`.</span><span class="sxs-lookup"><span data-stu-id="85760-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="85760-183">Quando um usuário é atualizado no banco de dados, o valor de `LastChanged` é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="85760-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="85760-184">Para invalidar um cookie quando o banco de dados é alterado com base no valor de `LastChanged`, crie o cookie com uma declaração de `LastChanged` que contém o valor `LastChanged` atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="85760-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="85760-185">Para implementar uma substituição para o evento `ValidatePrincipal`, escreva um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="85760-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="85760-186">Veja a seguir um exemplo de implementação de `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="85760-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="85760-187">Registre a instância de eventos durante o registro do serviço de cookie no método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="85760-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="85760-188">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para sua classe de `CustomCookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="85760-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="85760-189">Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="85760-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="85760-190">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a propriedade `context.ShouldRenew` como `true`.</span><span class="sxs-lookup"><span data-stu-id="85760-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="85760-191">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="85760-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="85760-192">A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="85760-193">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="85760-193">Persistent cookies</span></span>

<span data-ttu-id="85760-194">Talvez você queira que o cookie persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="85760-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="85760-195">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="85760-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="85760-196">O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="85760-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="85760-197">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="85760-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="85760-198">Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="85760-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="85760-199">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` no <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="85760-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="85760-200">Expiração de cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="85760-200">Absolute cookie expiration</span></span>

<span data-ttu-id="85760-201">Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="85760-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="85760-202">Para criar um cookie persistente, `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="85760-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="85760-203">Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="85760-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="85760-204">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, se definido.</span><span class="sxs-lookup"><span data-stu-id="85760-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="85760-205">O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="85760-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="85760-206">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="85760-206">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

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

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85760-207">A identidade de ASP.NET Core é um provedor de autenticação completo e repleto de recursos para criar e manter logons.</span><span class="sxs-lookup"><span data-stu-id="85760-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="85760-208">No entanto, um provedor de autenticação de autenticação baseada em cookie sem ASP.NET Core identidade pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="85760-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="85760-209">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="85760-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="85760-210">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="85760-210">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="85760-211">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="85760-212">Use o endereço de **Email** `maria.rodriguez@contoso.com` e qualquer senha para conectar o usuário.</span><span class="sxs-lookup"><span data-stu-id="85760-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="85760-213">O usuário é autenticado no método `AuthenticateUser` no arquivo *pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="85760-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="85760-214">Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="85760-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="85760-215">Configuração</span><span class="sxs-lookup"><span data-stu-id="85760-215">Configuration</span></span>

<span data-ttu-id="85760-216">Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="85760-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="85760-217">No método `Startup.ConfigureServices`, crie o serviço de middleware de autenticação com os métodos <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:</span><span class="sxs-lookup"><span data-stu-id="85760-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="85760-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="85760-219">`AuthenticationScheme` é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="85760-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="85760-220">Definir o `AuthenticationScheme` como [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="85760-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="85760-221">Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.</span><span class="sxs-lookup"><span data-stu-id="85760-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="85760-222">O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="85760-223">Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, ele usa `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").</span><span class="sxs-lookup"><span data-stu-id="85760-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="85760-224">A propriedade <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> do cookie de autenticação é definida como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="85760-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="85760-225">Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="85760-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="85760-226">Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="85760-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="85760-227">No método `Startup.Configure`, chame o método `UseAuthentication` para invocar o middleware de autenticação que define a propriedade `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="85760-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="85760-228">Chame o método `UseAuthentication` antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="85760-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="85760-229">A classe <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> é usada para configurar as opções do provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="85760-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="85760-230">Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="85760-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="85760-231">Middleware de política de cookie</span><span class="sxs-lookup"><span data-stu-id="85760-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="85760-232">O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie.</span><span class="sxs-lookup"><span data-stu-id="85760-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="85760-233">Adicionar o middleware ao pipeline de processamento de aplicativo é sensível à ordem&mdash;ele afeta apenas os componentes downstream registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="85760-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="85760-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido ao middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="85760-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="85760-235">O valor de <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> padrão é `SameSiteMode.Lax` para permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="85760-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="85760-236">Para impor estritamente uma política do mesmo site de `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="85760-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="85760-237">Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="85760-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="85760-238">A configuração de middleware de política de cookie para `MinimumSameSitePolicy` pode afetar a configuração de `Cookie.SameSite` nas configurações de `CookieAuthenticationOptions` de acordo com a matriz abaixo.</span><span class="sxs-lookup"><span data-stu-id="85760-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="85760-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="85760-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="85760-240">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="85760-240">Cookie.SameSite</span></span> | <span data-ttu-id="85760-241">Cookie resultante. configuração de SameSite</span><span class="sxs-lookup"><span data-stu-id="85760-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="85760-242">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-242">SameSiteMode.None</span></span>     | <span data-ttu-id="85760-243">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-243">SameSiteMode.None</span></span><br><span data-ttu-id="85760-244">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-245">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="85760-246">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-246">SameSiteMode.None</span></span><br><span data-ttu-id="85760-247">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-248">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="85760-249">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="85760-250">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-250">SameSiteMode.None</span></span><br><span data-ttu-id="85760-251">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-252">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="85760-253">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-254">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-255">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="85760-256">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="85760-257">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="85760-257">SameSiteMode.None</span></span><br><span data-ttu-id="85760-258">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="85760-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="85760-259">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="85760-260">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="85760-261">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="85760-262">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="85760-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="85760-263">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="85760-263">Create an authentication cookie</span></span>

<span data-ttu-id="85760-264">Para criar um cookie que contém informações do usuário, construa um <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="85760-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="85760-265">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="85760-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="85760-266">Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer <xref:System.Security.Claims.Claim>s necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:</span><span class="sxs-lookup"><span data-stu-id="85760-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="85760-267">`SignInAsync` cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="85760-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="85760-268">Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="85760-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="85760-269">O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="85760-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="85760-270">Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="85760-271">Sair</span><span class="sxs-lookup"><span data-stu-id="85760-271">Sign out</span></span>

<span data-ttu-id="85760-272">Para desconectar o usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="85760-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="85760-273">Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="85760-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="85760-274">Caso contrário, o esquema padrão será usado.</span><span class="sxs-lookup"><span data-stu-id="85760-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="85760-275">Reagir a alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="85760-275">React to back-end changes</span></span>

<span data-ttu-id="85760-276">Depois que um cookie é criado, o cookie é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="85760-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="85760-277">Se uma conta de usuário estiver desabilitada em sistemas back-end:</span><span class="sxs-lookup"><span data-stu-id="85760-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="85760-278">O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="85760-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="85760-279">O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.</span><span class="sxs-lookup"><span data-stu-id="85760-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="85760-280">O evento <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="85760-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="85760-281">A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="85760-282">Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="85760-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="85760-283">Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido.</span><span class="sxs-lookup"><span data-stu-id="85760-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="85760-284">No aplicativo de exemplo, o banco de dados é implementado em `IUserRepository` e armazena um valor de `LastChanged`.</span><span class="sxs-lookup"><span data-stu-id="85760-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="85760-285">Quando um usuário é atualizado no banco de dados, o valor de `LastChanged` é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="85760-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="85760-286">Para invalidar um cookie quando o banco de dados é alterado com base no valor de `LastChanged`, crie o cookie com uma declaração de `LastChanged` que contém o valor `LastChanged` atual do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="85760-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="85760-287">Para implementar uma substituição para o evento `ValidatePrincipal`, escreva um método com a seguinte assinatura em uma classe derivada de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="85760-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="85760-288">Veja a seguir um exemplo de implementação de `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="85760-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="85760-289">Registre a instância de eventos durante o registro do serviço de cookie no método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="85760-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="85760-290">Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para sua classe de `CustomCookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="85760-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="85760-291">Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de nenhuma forma.</span><span class="sxs-lookup"><span data-stu-id="85760-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="85760-292">Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a propriedade `context.ShouldRenew` como `true`.</span><span class="sxs-lookup"><span data-stu-id="85760-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="85760-293">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="85760-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="85760-294">A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="85760-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="85760-295">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="85760-295">Persistent cookies</span></span>

<span data-ttu-id="85760-296">Talvez você queira que o cookie persista entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="85760-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="85760-297">Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="85760-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="85760-298">O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador.</span><span class="sxs-lookup"><span data-stu-id="85760-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="85760-299">Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas.</span><span class="sxs-lookup"><span data-stu-id="85760-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="85760-300">Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="85760-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="85760-301">Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` no <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="85760-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="85760-302">Expiração de cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="85760-302">Absolute cookie expiration</span></span>

<span data-ttu-id="85760-303">Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="85760-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="85760-304">Para criar um cookie persistente, `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="85760-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="85760-305">Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="85760-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="85760-306">Quando `ExpiresUtc` é definido, ele substitui o valor da <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se definido.</span><span class="sxs-lookup"><span data-stu-id="85760-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="85760-307">O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="85760-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="85760-308">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="85760-308">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

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

## <a name="additional-resources"></a><span data-ttu-id="85760-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="85760-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="85760-310">Verificações de função baseadas em política</span><span class="sxs-lookup"><span data-stu-id="85760-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
