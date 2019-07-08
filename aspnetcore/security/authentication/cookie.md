---
title: Usar autenticação de cookie sem o ASP.NET Core Identity
author: rick-anderson
description: Saiba como usar a autenticação de cookie sem o ASP.NET Core Identity.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622743"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="dd15b-103">Usar autenticação de cookie sem o ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="dd15b-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="dd15b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dd15b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dd15b-105">ASP.NET Core Identity é um provedor de autenticação completa e a versão completa para criar e manter os logons.</span><span class="sxs-lookup"><span data-stu-id="dd15b-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="dd15b-106">No entanto, um provedor de autenticação da autenticação baseada em cookie sem o ASP.NET Core Identity pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="dd15b-106">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="dd15b-107">Para obter mais informações, consulte <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="dd15b-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="dd15b-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd15b-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="dd15b-109">Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd15b-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="dd15b-110">Use o **E-mail** nome de usuário `maria.rodriguez@contoso.com` e nenhuma senha para a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="dd15b-110">Use the **Email** user name `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="dd15b-111">O usuário é autenticado na `AuthenticateUser` método na *Pages/Account/Login.cshtml.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="dd15b-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="dd15b-112">Em um exemplo do mundo real, o usuário deve ser autenticado em relação a um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="dd15b-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="dd15b-113">Configuração</span><span class="sxs-lookup"><span data-stu-id="dd15b-113">Configuration</span></span>

<span data-ttu-id="dd15b-114">Se o aplicativo não usa o [metapacote do Microsoft](xref:fundamentals/metapackage-app), criar uma referência de pacote no arquivo de projeto para o [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) pacote.</span><span class="sxs-lookup"><span data-stu-id="dd15b-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="dd15b-115">No `Startup.ConfigureServices` método, crie o serviço de Middleware de autenticação com o <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos:</span><span class="sxs-lookup"><span data-stu-id="dd15b-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="dd15b-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd15b-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="dd15b-117">`AuthenticationScheme` é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="dd15b-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="dd15b-118">Definindo o `AuthenticationScheme` à [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "Cookies" para o esquema.</span><span class="sxs-lookup"><span data-stu-id="dd15b-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="dd15b-119">Você pode fornecer qualquer valor de cadeia de caracteres que diferencia o esquema.</span><span class="sxs-lookup"><span data-stu-id="dd15b-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="dd15b-120">Esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd15b-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="dd15b-121">Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, ele usa `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span><span class="sxs-lookup"><span data-stu-id="dd15b-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="dd15b-122">O cookie de autenticação <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> estiver definida como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="dd15b-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="dd15b-123">Cookies de autenticação são permitidos quando um visitante do site não tiver consentido para coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="dd15b-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="dd15b-124">Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="dd15b-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="dd15b-125">No `Startup.Configure` método, a chamada a `UseAuthentication` método para invocar o Middleware de autenticação define o `HttpContext.User` propriedade.</span><span class="sxs-lookup"><span data-stu-id="dd15b-125">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="dd15b-126">Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="dd15b-126">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="dd15b-127">O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções de provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dd15b-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="dd15b-128">Definir `CookieAuthenticationOptions` na configuração do serviço para autenticação no `Startup.ConfigureServices` método:</span><span class="sxs-lookup"><span data-stu-id="dd15b-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="dd15b-129">Cookie de Middleware de política</span><span class="sxs-lookup"><span data-stu-id="dd15b-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="dd15b-130">[Middleware do cookie política](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie.</span><span class="sxs-lookup"><span data-stu-id="dd15b-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="dd15b-131">Adicionar o middleware ao pipeline de processamento de aplicativos é a ordem confidencial&mdash;ele afeta somente os componentes downstream, registrados no pipeline.</span><span class="sxs-lookup"><span data-stu-id="dd15b-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="dd15b-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o Cookie de Middleware de política para controlar características globais do processamento de cookie e gancho em manipuladores de processamento do cookie quando os cookies são acrescentados ou excluídos.</span><span class="sxs-lookup"><span data-stu-id="dd15b-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="dd15b-133">O padrão <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor é `SameSiteMode.Lax` para permitir a autenticação OAuth2.</span><span class="sxs-lookup"><span data-stu-id="dd15b-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="dd15b-134">Estritamente impor uma política do mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="dd15b-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="dd15b-135">Embora essa configuração interrompe OAuth2 e outras esquemas de autenticação entre origens, ela eleva o nível de segurança do cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.</span><span class="sxs-lookup"><span data-stu-id="dd15b-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="dd15b-136">A configuração de Middleware de política de Cookie para `MinimumSameSitePolicy` podem afetar a definição de `Cookie.SameSite` em `CookieAuthenticationOptions` configurações de acordo com a matriz a seguir.</span><span class="sxs-lookup"><span data-stu-id="dd15b-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="dd15b-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="dd15b-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="dd15b-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="dd15b-138">Cookie.SameSite</span></span> | <span data-ttu-id="dd15b-139">Configuração de Cookie.SameSite resultante</span><span class="sxs-lookup"><span data-stu-id="dd15b-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="dd15b-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="dd15b-140">SameSiteMode.None</span></span>     | <span data-ttu-id="dd15b-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="dd15b-141">SameSiteMode.None</span></span><br><span data-ttu-id="dd15b-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="dd15b-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="dd15b-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="dd15b-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="dd15b-144">SameSiteMode.None</span></span><br><span data-ttu-id="dd15b-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="dd15b-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="dd15b-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="dd15b-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="dd15b-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="dd15b-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="dd15b-148">SameSiteMode.None</span></span><br><span data-ttu-id="dd15b-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="dd15b-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="dd15b-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="dd15b-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="dd15b-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="dd15b-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="dd15b-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="dd15b-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="dd15b-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="dd15b-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="dd15b-155">SameSiteMode.None</span></span><br><span data-ttu-id="dd15b-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="dd15b-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="dd15b-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="dd15b-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="dd15b-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="dd15b-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="dd15b-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="dd15b-161">Criar um cookie de autenticação</span><span class="sxs-lookup"><span data-stu-id="dd15b-161">Create an authentication cookie</span></span>

<span data-ttu-id="dd15b-162">Para criar um cookie contendo informações de usuário, construir um <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="dd15b-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="dd15b-163">As informações do usuário são serializadas e armazenadas no cookie.</span><span class="sxs-lookup"><span data-stu-id="dd15b-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="dd15b-164">Criar uma <xref:System.Security.Claims.ClaimsIdentity> com qualquer necessária <xref:System.Security.Claims.Claim>s e chamada <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para a entrada do usuário:</span><span class="sxs-lookup"><span data-stu-id="dd15b-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="dd15b-165">`SignInAsync` cria um cookie criptografado e o adiciona à resposta atual.</span><span class="sxs-lookup"><span data-stu-id="dd15b-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="dd15b-166">Se `AuthenticationScheme` não for especificado, o esquema padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="dd15b-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="dd15b-167">ASP.NET Core [proteção de dados](xref:security/data-protection/using-data-protection) sistema é usado para criptografia.</span><span class="sxs-lookup"><span data-stu-id="dd15b-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="dd15b-168">Para um aplicativo hospedado em várias máquinas, carregar balanceamento entre aplicativos, ou usando uma web farm [configurar a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo anel de chave e o identificador do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd15b-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="dd15b-169">Sair</span><span class="sxs-lookup"><span data-stu-id="dd15b-169">Sign out</span></span>

<span data-ttu-id="dd15b-170">Para desconectar o usuário atual e excluir seus cookies, chamar <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="dd15b-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="dd15b-171">Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "Cookies") não é usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dd15b-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="dd15b-172">Caso contrário, o esquema padrão é usado.</span><span class="sxs-lookup"><span data-stu-id="dd15b-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="dd15b-173">Reagir às alterações de back-end</span><span class="sxs-lookup"><span data-stu-id="dd15b-173">React to back-end changes</span></span>

<span data-ttu-id="dd15b-174">Depois que um cookie é criado, o cookie é a única fonte de identidade.</span><span class="sxs-lookup"><span data-stu-id="dd15b-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="dd15b-175">Se uma conta de usuário é desabilitada nos sistemas de back-end:</span><span class="sxs-lookup"><span data-stu-id="dd15b-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="dd15b-176">Sistema de autenticação de cookie do aplicativo continua a processar solicitações com base no cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dd15b-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="dd15b-177">O usuário permanece conectado ao aplicativo, desde que o cookie de autenticação é válido.</span><span class="sxs-lookup"><span data-stu-id="dd15b-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="dd15b-178">O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie.</span><span class="sxs-lookup"><span data-stu-id="dd15b-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="dd15b-179">Validar o cookie em cada solicitação minimiza o risco de usuários revogados acessando o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd15b-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="dd15b-180">Uma abordagem de validação de cookie baseia-se em manter o controle de quando o banco de dados do usuário é alterado.</span><span class="sxs-lookup"><span data-stu-id="dd15b-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="dd15b-181">Se o banco de dados não foi alterado desde que o cookie do usuário foi emitido, não é necessário para autenticar o usuário novamente se o cookie ainda é válido.</span><span class="sxs-lookup"><span data-stu-id="dd15b-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="dd15b-182">No aplicativo de exemplo, o banco de dados é implementado no `IUserRepository` e armazena um `LastChanged` valor.</span><span class="sxs-lookup"><span data-stu-id="dd15b-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="dd15b-183">Quando um usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.</span><span class="sxs-lookup"><span data-stu-id="dd15b-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="dd15b-184">Para invalidar um cookie, quando as alterações de banco de dados com base nas `LastChanged` o valor, criar o cookie com um `LastChanged` contendo atual de declaração `LastChanged` valor do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="dd15b-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="dd15b-185">Para implementar uma substituição para o `ValidatePrincipal` eventos, escreva um método com a assinatura a seguir em uma classe que deriva de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="dd15b-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="dd15b-186">A seguir está um exemplo de implementação de `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="dd15b-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="dd15b-187">Registrar a instância de eventos durante o registro do serviço de cookie no `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="dd15b-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dd15b-188">Fornecer um [com escopo de registro do serviço](xref:fundamentals/dependency-injection#service-lifetimes) para seu `CustomCookieAuthenticationEvents` classe:</span><span class="sxs-lookup"><span data-stu-id="dd15b-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="dd15b-189">Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de qualquer forma.</span><span class="sxs-lookup"><span data-stu-id="dd15b-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="dd15b-190">Se você quiser atualizar forma não destrutiva a entidade de usuário, chame `context.ReplacePrincipal` e defina o `context.ShouldRenew` propriedade `true`.</span><span class="sxs-lookup"><span data-stu-id="dd15b-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="dd15b-191">A abordagem descrita aqui é disparada em cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="dd15b-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="dd15b-192">Validar os cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma penalidade de desempenho grande para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dd15b-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="dd15b-193">Cookies persistentes</span><span class="sxs-lookup"><span data-stu-id="dd15b-193">Persistent cookies</span></span>

<span data-ttu-id="dd15b-194">Talvez você queira que o cookie para persistir entre as sessões do navegador.</span><span class="sxs-lookup"><span data-stu-id="dd15b-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="dd15b-195">Essa persistência deve ser habilitada apenas com o consentimento explícito do usuário com uma caixa de seleção "Lembrar-Me" na entrada ou um mecanismo semelhante.</span><span class="sxs-lookup"><span data-stu-id="dd15b-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="dd15b-196">O trecho de código a seguir cria uma identidade e o cookie correspondente que sobrevive a por meio de fechamentos de navegador.</span><span class="sxs-lookup"><span data-stu-id="dd15b-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="dd15b-197">Quaisquer configurações de expiração deslizante configuradas anteriormente são consideradas.</span><span class="sxs-lookup"><span data-stu-id="dd15b-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="dd15b-198">Se o cookie expirar enquanto o navegador é fechado, o navegador limpa o cookie depois que ele seja reiniciado.</span><span class="sxs-lookup"><span data-stu-id="dd15b-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="dd15b-199">Definir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> à `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="dd15b-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="dd15b-200">Expiração do cookie absoluto</span><span class="sxs-lookup"><span data-stu-id="dd15b-200">Absolute cookie expiration</span></span>

<span data-ttu-id="dd15b-201">Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="dd15b-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="dd15b-202">Para criar um cookie persistente, `IsPersistent` também deve ser definido.</span><span class="sxs-lookup"><span data-stu-id="dd15b-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="dd15b-203">Caso contrário, o cookie é criado com um tempo de vida com base em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém.</span><span class="sxs-lookup"><span data-stu-id="dd15b-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="dd15b-204">Quando `ExpiresUtc` estiver definido, ele substitui o valor a <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se definido.</span><span class="sxs-lookup"><span data-stu-id="dd15b-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="dd15b-205">O trecho de código a seguir cria uma identidade e o cookie correspondente que dura por 20 minutos.</span><span class="sxs-lookup"><span data-stu-id="dd15b-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="dd15b-206">Isso ignora as configurações de expiração deslizante configuradas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="dd15b-206">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="dd15b-207">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dd15b-207">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="dd15b-208">Verificações de função baseado em políticas</span><span class="sxs-lookup"><span data-stu-id="dd15b-208">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
