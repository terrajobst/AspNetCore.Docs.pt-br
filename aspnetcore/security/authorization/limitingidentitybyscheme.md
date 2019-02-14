---
title: Autorizar com um esquema específico no ASP.NET Core
author: rick-anderson
description: Este artigo explica como limitar a identidade de um esquema específico ao trabalhar com vários métodos de autenticação.
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248193"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="694e4-103">Autorizar com um esquema específico no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="694e4-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="694e4-104">Em alguns cenários, como aplicativos de página única (SPAs), é comum usar vários métodos de autenticação.</span><span class="sxs-lookup"><span data-stu-id="694e4-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="694e4-105">Por exemplo, o aplicativo pode usar a autenticação baseada em cookies para fazer logon e autenticação do portador do JWT para solicitações de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="694e4-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="694e4-106">Em alguns casos, o aplicativo pode ter várias instâncias de um manipulador de autenticação.</span><span class="sxs-lookup"><span data-stu-id="694e4-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="694e4-107">Por exemplo, dois manipuladores de cookie em que um contém uma identidade básica e o outro é criado quando a autenticação multifator (MFA) foi acionada.</span><span class="sxs-lookup"><span data-stu-id="694e4-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="694e4-108">MFA pode ser acionado porque o usuário solicitou uma operação que exige segurança extra.</span><span class="sxs-lookup"><span data-stu-id="694e4-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="694e4-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="694e4-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="694e4-110">Um esquema de autenticação é chamado quando o serviço de autenticação é configurado durante a autenticação.</span><span class="sxs-lookup"><span data-stu-id="694e4-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="694e4-111">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="694e4-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="694e4-112">No código anterior, foram adicionados dois manipuladores de autenticação: um para cookies e outro para o portador.</span><span class="sxs-lookup"><span data-stu-id="694e4-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="694e4-113">Especificar o esquema padrão resulta no `HttpContext.User` propriedade sendo definida como essa identidade.</span><span class="sxs-lookup"><span data-stu-id="694e4-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="694e4-114">Se esse comportamento não for desejado, desabilitá-lo, invocando o formulário sem parâmetros de `AddAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="694e4-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="694e4-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="694e4-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="694e4-116">Esquemas de autenticação são nomeadas quando middlewares de autenticação são configurados durante a autenticação.</span><span class="sxs-lookup"><span data-stu-id="694e4-116">Authentication schemes are named when authentication middlewares are configured during authentication.</span></span> <span data-ttu-id="694e4-117">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="694e4-117">For example:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

<span data-ttu-id="694e4-118">No código anterior, foram adicionados dois middlewares de autenticação: um para cookies e outro para o portador.</span><span class="sxs-lookup"><span data-stu-id="694e4-118">In the preceding code, two authentication middlewares have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="694e4-119">Especificar o esquema padrão resulta no `HttpContext.User` propriedade sendo definida como essa identidade.</span><span class="sxs-lookup"><span data-stu-id="694e4-119">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="694e4-120">Se esse comportamento não for desejado, desabilitá-lo definindo a `AuthenticationOptions.AutomaticAuthenticate` propriedade para `false`.</span><span class="sxs-lookup"><span data-stu-id="694e4-120">If that behavior isn't desired, disable it by setting the `AuthenticationOptions.AutomaticAuthenticate` property to `false`.</span></span>

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="694e4-121">Selecionar o esquema com o atributo Authorize</span><span class="sxs-lookup"><span data-stu-id="694e4-121">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="694e4-122">No ponto de autorização, o aplicativo indica o manipulador a ser usado.</span><span class="sxs-lookup"><span data-stu-id="694e4-122">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="694e4-123">Selecione o manipulador com a qual o aplicativo autorizará passando uma lista delimitada por vírgulas de esquemas de autenticação para `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="694e4-123">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="694e4-124">O `[Authorize]` atributo especifica o esquema de autenticação ou esquemas de usar, independentemente de um padrão é configurado.</span><span class="sxs-lookup"><span data-stu-id="694e4-124">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="694e4-125">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="694e4-125">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="694e4-126">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="694e4-126">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="694e4-127">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="694e4-127">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

---

<span data-ttu-id="694e4-128">No exemplo anterior, o cookie e o portador manipuladores execute em tenham a oportunidade de criar e acrescentar uma identidade para o usuário atual.</span><span class="sxs-lookup"><span data-stu-id="694e4-128">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="694e4-129">Ao especificar um único esquema, o manipulador correspondente é executado.</span><span class="sxs-lookup"><span data-stu-id="694e4-129">By specifying a single scheme only, the corresponding handler runs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="694e4-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="694e4-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="694e4-131">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="694e4-131">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

<span data-ttu-id="694e4-132">No código anterior, somente o manipulador com o esquema de "Bearer" é executado.</span><span class="sxs-lookup"><span data-stu-id="694e4-132">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="694e4-133">Todas as identidades baseadas em cookies serão ignoradas.</span><span class="sxs-lookup"><span data-stu-id="694e4-133">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="694e4-134">Selecionar o esquema com as políticas</span><span class="sxs-lookup"><span data-stu-id="694e4-134">Selecting the scheme with policies</span></span>

<span data-ttu-id="694e4-135">Se você preferir especificar os esquemas de desejada [diretiva](xref:security/authorization/policies), você pode definir o `AuthenticationSchemes` coleção ao adicionar sua política:</span><span class="sxs-lookup"><span data-stu-id="694e4-135">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="694e4-136">No exemplo anterior, a política de "Over18" só é executada em relação a identidade criada pelo manipulador de "Portador".</span><span class="sxs-lookup"><span data-stu-id="694e4-136">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="694e4-137">Usar a política, definindo o `[Authorize]` do atributo `Policy` propriedade:</span><span class="sxs-lookup"><span data-stu-id="694e4-137">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="694e4-138">Usar vários esquemas de autenticação</span><span class="sxs-lookup"><span data-stu-id="694e4-138">Use multiple authentication schemes</span></span>

<span data-ttu-id="694e4-139">Alguns aplicativos talvez seja necessário dar suporte a vários tipos de autenticação.</span><span class="sxs-lookup"><span data-stu-id="694e4-139">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="694e4-140">Por exemplo, seu aplicativo pode autenticar os usuários do Active Directory do Azure e de um banco de dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="694e4-140">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="694e4-141">Outro exemplo é um aplicativo que autentica os usuários do Active Directory Federation Services e o Azure Active Directory B2C.</span><span class="sxs-lookup"><span data-stu-id="694e4-141">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="694e4-142">Nesse caso, o aplicativo deve aceitar um token de portador JWT de vários emissores.</span><span class="sxs-lookup"><span data-stu-id="694e4-142">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="694e4-143">Adicione todos os esquemas de autenticação que você gostaria de aceitar.</span><span class="sxs-lookup"><span data-stu-id="694e4-143">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="694e4-144">Por exemplo, o código a seguir no `Startup.ConfigureServices` adiciona dois esquemas de autenticação de portador JWT com emissores diferentes:</span><span class="sxs-lookup"><span data-stu-id="694e4-144">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="694e4-145">Apenas uma autenticação de portador JWT está registrada com o esquema de autenticação padrão `JwtBearerDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="694e4-145">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="694e4-146">Autenticação adicional deve ser registrado com um esquema de autenticação exclusiva.</span><span class="sxs-lookup"><span data-stu-id="694e4-146">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="694e4-147">A próxima etapa é atualizar a política de autorização padrão para aceitar os dois esquemas de autenticação.</span><span class="sxs-lookup"><span data-stu-id="694e4-147">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="694e4-148">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="694e4-148">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="694e4-149">Como a política de autorização padrão é substituída, é possível usar o `[Authorize]` atributo em controladores.</span><span class="sxs-lookup"><span data-stu-id="694e4-149">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="694e4-150">O controlador, em seguida, aceita solicitações com JWT emitido pelo emissor do primeiro ou segundo.</span><span class="sxs-lookup"><span data-stu-id="694e4-150">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
