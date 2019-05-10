---
title: Autorizar com um esquema específico no ASP.NET Core
author: rick-anderson
description: Este artigo explica como limitar a identidade de um esquema específico ao trabalhar com vários métodos de autenticação.
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897333"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autorizar com um esquema específico no ASP.NET Core

Em alguns cenários, como aplicativos de página única (SPAs), é comum usar vários métodos de autenticação. Por exemplo, o aplicativo pode usar a autenticação baseada em cookies para fazer logon e autenticação do portador do JWT para solicitações de JavaScript. Em alguns casos, o aplicativo pode ter várias instâncias de um manipulador de autenticação. Por exemplo, dois manipuladores de cookie em que um contém uma identidade básica e o outro é criado quando a autenticação multifator (MFA) foi acionada. MFA pode ser acionado porque o usuário solicitou uma operação que exige segurança extra.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Um esquema de autenticação é chamado quando o serviço de autenticação é configurado durante a autenticação. Por exemplo:

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

No código anterior, foram adicionados dois manipuladores de autenticação: um para cookies e outro para o portador.

>[!NOTE]
>Especificar o esquema padrão resulta no `HttpContext.User` propriedade sendo definida como essa identidade. Se esse comportamento não for desejado, desabilitá-lo, invocando o formulário sem parâmetros de `AddAuthentication`.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Esquemas de autenticação são nomeadas quando middlewares de autenticação são configurados durante a autenticação. Por exemplo:

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

No código anterior, foram adicionados dois middlewares de autenticação: um para cookies e outro para o portador.

>[!NOTE]
>Especificar o esquema padrão resulta no `HttpContext.User` propriedade sendo definida como essa identidade. Se esse comportamento não for desejado, desabilitá-lo definindo a `AuthenticationOptions.AutomaticAuthenticate` propriedade para `false`.

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Selecionar o esquema com o atributo Authorize

No ponto de autorização, o aplicativo indica o manipulador a ser usado. Selecione o manipulador com a qual o aplicativo autorizará passando uma lista delimitada por vírgulas de esquemas de autenticação para `[Authorize]`. O `[Authorize]` atributo especifica o esquema de autenticação ou esquemas de usar, independentemente de um padrão é configurado. Por exemplo:

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

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

No exemplo anterior, o cookie e o portador manipuladores execute em tenham a oportunidade de criar e acrescentar uma identidade para o usuário atual. Ao especificar um único esquema, o manipulador correspondente é executado.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

No código anterior, somente o manipulador com o esquema de "Bearer" é executado. Todas as identidades baseadas em cookies serão ignoradas.

## <a name="selecting-the-scheme-with-policies"></a>Selecionar o esquema com as políticas

Se você preferir especificar os esquemas de desejada [diretiva](xref:security/authorization/policies), você pode definir o `AuthenticationSchemes` coleção ao adicionar sua política:

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

No exemplo anterior, a política de "Over18" só é executada em relação a identidade criada pelo manipulador de "Portador". Usar a política, definindo o `[Authorize]` do atributo `Policy` propriedade:

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Usar vários esquemas de autenticação

Alguns aplicativos talvez seja necessário dar suporte a vários tipos de autenticação. Por exemplo, seu aplicativo pode autenticar os usuários do Active Directory do Azure e de um banco de dados de usuários. Outro exemplo é um aplicativo que autentica os usuários do Active Directory Federation Services e o Azure Active Directory B2C. Nesse caso, o aplicativo deve aceitar um token de portador JWT de vários emissores.

Adicione todos os esquemas de autenticação que você gostaria de aceitar. Por exemplo, o código a seguir no `Startup.ConfigureServices` adiciona dois esquemas de autenticação de portador JWT com emissores diferentes:

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
> Apenas uma autenticação de portador JWT está registrada com o esquema de autenticação padrão `JwtBearerDefaults.AuthenticationScheme`. Autenticação adicional deve ser registrado com um esquema de autenticação exclusiva.

A próxima etapa é atualizar a política de autorização padrão para aceitar os dois esquemas de autenticação. Por exemplo:

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

Como a política de autorização padrão é substituída, é possível usar o `[Authorize]` atributo em controladores. O controlador, em seguida, aceita solicitações com JWT emitido pelo emissor do primeiro ou segundo.

::: moniker-end
