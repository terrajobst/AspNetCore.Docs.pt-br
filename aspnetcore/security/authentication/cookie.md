---
title: Usar autenticação de cookie sem identidade ASP.NET Core
author: rick-anderson
description: Saiba como usar a autenticação de cookie sem ASP.NET Core identidade.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
uid: security/authentication/cookie
ms.openlocfilehash: 76c7fc20c8870668ca7c65d975e2ed59f40f7dc8
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384822"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Usar autenticação de cookie sem identidade ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

A identidade de ASP.NET Core é um provedor de autenticação completo e repleto de recursos para criar e manter logons. No entanto, um provedor de autenticação de autenticação baseada em cookie sem ASP.NET Core identidade pode ser usado. Para obter mais informações, consulte <xref:security/authentication/identity>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))

Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo. Use o endereço `maria.rodriguez@contoso.com` de email e qualquer senha para conectar o usuário. O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* . Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.

## <a name="configuration"></a>Configuração

Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

No método, crie os serviços de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> métodos <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e: `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo. `AuthenticationScheme`é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme). A definição `AuthenticationScheme` de para [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema. Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.

O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo. Quando um esquema de autenticação de cookie não <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>é fornecido para `CookieAuthenticationDefaults.AuthenticationScheme` , ele usa ("cookies").

A propriedade do <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> cookie de autenticação é definida `true` como por padrão. Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados. Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.

No `Startup.Configure`, chame `UseAuthentication` e `UseAuthorization` para definir a `HttpContext.User` Propriedade e executar o middleware de autorização para solicitações. Chame os `UseAuthentication` métodos `UseAuthorization` e antes de `UseEndpoints`chamar:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

A <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.

Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação `Startup.ConfigureServices` no método:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Middleware de política de cookie

O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie. A adição do middleware ao pipeline de processamento do aplicativo é a&mdash;diferenciação de ordem que afeta apenas os componentes de downstream registrados no pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.

O valor <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2. Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`. Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

A configuração de middleware de política de `MinimumSameSitePolicy` cookie para pode afetar a `Cookie.SameSite` configuração `CookieAuthenticationOptions` de em configurações de acordo com a matriz abaixo.

| MinimumSameSitePolicy | Cookie.SameSite | Cookie resultante. configuração de SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Criar um cookie de autenticação

Para criar um cookie que contém informações do usuário, <xref:System.Security.Claims.ClaimsPrincipal>Construa um. As informações do usuário são serializadas e armazenadas no cookie. 

Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer s <xref:System.Security.Claims.Claim>necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`Cria um cookie criptografado e o adiciona à resposta atual. Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.

O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia. Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.

## <a name="sign-out"></a>Sair

Para sair do usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação. Caso contrário, o esquema padrão será usado.

## <a name="react-to-back-end-changes"></a>Reagir a alterações de back-end

Depois que um cookie é criado, o cookie é a única fonte de identidade. Se uma conta de usuário estiver desabilitada em sistemas back-end:

* O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.
* O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.

O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie. A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.

Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado. Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido. No aplicativo de exemplo, o banco de dados é `IUserRepository` implementado no e `LastChanged` armazena um valor. Quando um usuário é atualizado no banco de dados, `LastChanged` o valor é definido como a hora atual.

Para invalidar um cookie quando o banco de dados é alterado com base no `LastChanged` valor, crie o cookie com uma `LastChanged` declaração contendo o valor `LastChanged` atual do banco de dados:

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

Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de: <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Veja a seguir um exemplo de implementação `CookieAuthenticationEvents`de:

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

Registre a instância de eventos durante o `Startup.ConfigureServices` registro do serviço de cookie no método. Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para `CustomCookieAuthenticationEvents` sua classe:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de nenhuma forma. Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` Propriedade como `true`.

> [!WARNING]
> A abordagem descrita aqui é disparada em cada solicitação. A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.

## <a name="persistent-cookies"></a>Cookies persistentes

Talvez você queira que o cookie persista entre as sessões do navegador. Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante. 

O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador. Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas. Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.

Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em :<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

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

## <a name="absolute-cookie-expiration"></a>Expiração de cookie absoluto

Um tempo de expiração absoluto pode ser <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>definido com. Para criar um cookie persistente, `IsPersistent` também deve ser definido. Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém. Quando `ExpiresUtc` é definido, ele substitui o valor <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> da opção de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se definido.

O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos. Isso ignora as configurações de expiração deslizante configuradas anteriormente.

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

A identidade de ASP.NET Core é um provedor de autenticação completo e repleto de recursos para criar e manter logons. No entanto, um provedor de autenticação de autenticação baseada em cookie sem ASP.NET Core identidade pode ser usado. Para obter mais informações, consulte <xref:security/authentication/identity>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))

Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificada no aplicativo. Use o endereço `maria.rodriguez@contoso.com` de email e qualquer senha para conectar o usuário. O usuário é autenticado no `AuthenticateUser` método no arquivo *pages/Account/Login. cshtml. cs* . Em um exemplo do mundo real, o usuário seria autenticado em um banco de dados.

## <a name="configuration"></a>Configuração

Se o aplicativo não usar o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), crie uma referência de pacote no arquivo de projeto para o pacote [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

No método, crie o serviço de middleware de autenticação com os <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> métodos <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>e: `Startup.ConfigureServices`

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo. `AuthenticationScheme`é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme). A definição `AuthenticationScheme` de para [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "cookies" para o esquema. Você pode fornecer qualquer valor de cadeia de caracteres que diferencie o esquema.

O esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo. Quando um esquema de autenticação de cookie não <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>é fornecido para `CookieAuthenticationDefaults.AuthenticationScheme` , ele usa ("cookies").

A propriedade do <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> cookie de autenticação é definida `true` como por padrão. Os cookies de autenticação são permitidos quando um visitante do site não consentiu na coleta de dados. Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.

No método, chame o `UseAuthentication` método para invocar o middleware de autenticação que define a `HttpContext.User` propriedade. `Startup.Configure` Chame o `UseAuthentication` método antes de `UseMvcWithDefaultRoute` chamar `UseMvc`ou:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

A <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções do provedor de autenticação.

Defina `CookieAuthenticationOptions` na configuração de serviço para autenticação `Startup.ConfigureServices` no método:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Middleware de política de cookie

O [middleware de política de cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie. A adição do middleware ao pipeline de processamento do aplicativo é a&mdash;diferenciação de ordem que afeta apenas os componentes de downstream registrados no pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o middleware de política de cookie para controlar as características globais do processamento de cookies e conectar-se a manipuladores de processamento de cookies quando os cookies são anexados ou excluídos.

O valor <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> padrão é `SameSiteMode.Lax` permitir a autenticação OAuth2. Para impor estritamente uma política de mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`. Embora essa configuração quebre OAuth2 e outros esquemas de autenticação entre origens, ela eleva o nível de segurança de cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

A configuração de middleware de política de `MinimumSameSitePolicy` cookie para pode afetar a `Cookie.SameSite` configuração `CookieAuthenticationOptions` de em configurações de acordo com a matriz abaixo.

| MinimumSameSitePolicy | Cookie.SameSite | Cookie resultante. configuração de SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Criar um cookie de autenticação

Para criar um cookie que contém informações do usuário, <xref:System.Security.Claims.ClaimsPrincipal>Construa um. As informações do usuário são serializadas e armazenadas no cookie. 

Crie um <xref:System.Security.Claims.ClaimsIdentity> com qualquer s <xref:System.Security.Claims.Claim>necessário e chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para entrar no usuário:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync`Cria um cookie criptografado e o adiciona à resposta atual. Se `AuthenticationScheme` não for especificado, o esquema padrão será usado.

O sistema de [proteção de dados](xref:security/data-protection/using-data-protection) de ASP.NET Core é usado para criptografia. Para um aplicativo hospedado em vários computadores, balanceamento de carga entre aplicativos ou usando um web farm, [Configure a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo token de chave e identificador de aplicativo.

## <a name="sign-out"></a>Sair

Para sair do usuário atual e excluir seu cookie, chame <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "cookies") não for usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação. Caso contrário, o esquema padrão será usado.

## <a name="react-to-back-end-changes"></a>Reagir a alterações de back-end

Depois que um cookie é criado, o cookie é a única fonte de identidade. Se uma conta de usuário estiver desabilitada em sistemas back-end:

* O sistema de autenticação de cookies do aplicativo continua processando solicitações com base no cookie de autenticação.
* O usuário permanece conectado ao aplicativo, contanto que o cookie de autenticação seja válido.

O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie. A validação do cookie em cada solicitação reduz o risco de usuários revogados acessarem o aplicativo.

Uma abordagem para a validação de cookie baseia-se no controle de quando o banco de dados do usuário é alterado. Se o banco de dados não tiver sido alterado desde que o cookie do usuário foi emitido, não haverá necessidade de autenticar novamente o usuário se o cookie ainda for válido. No aplicativo de exemplo, o banco de dados é `IUserRepository` implementado no e `LastChanged` armazena um valor. Quando um usuário é atualizado no banco de dados, `LastChanged` o valor é definido como a hora atual.

Para invalidar um cookie quando o banco de dados é alterado com base no `LastChanged` valor, crie o cookie com uma `LastChanged` declaração contendo o valor `LastChanged` atual do banco de dados:

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

Para implementar uma substituição para o `ValidatePrincipal` evento, grave um método com a seguinte assinatura em uma classe derivada de: <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Veja a seguir um exemplo de implementação `CookieAuthenticationEvents`de:

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

Registre a instância de eventos durante o `Startup.ConfigureServices` registro do serviço de cookie no método. Forneça um [registro de serviço com escopo](xref:fundamentals/dependency-injection#service-lifetimes) para `CustomCookieAuthenticationEvents` sua classe:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de nenhuma forma. Se você quiser atualizar não destrutivamente a entidade de usuário, chame `context.ReplacePrincipal` e defina a `context.ShouldRenew` Propriedade como `true`.

> [!WARNING]
> A abordagem descrita aqui é disparada em cada solicitação. A validação de cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma grande penalidade de desempenho para o aplicativo.

## <a name="persistent-cookies"></a>Cookies persistentes

Talvez você queira que o cookie persista entre as sessões do navegador. Essa persistência só deve ser habilitada com consentimento de usuário explícito com uma caixa de seleção "lembrar-me" na entrada ou em um mecanismo semelhante. 

O trecho de código a seguir cria uma identidade e um cookie correspondente que sobreviver pelos fechamentos do navegador. Todas as configurações de expiração deslizante configuradas anteriormente são respeitadas. Se o cookie expirar enquanto o navegador estiver fechado, o navegador limpará o cookie depois que ele for reiniciado.

Defina <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> como `true` em :<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>

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

## <a name="absolute-cookie-expiration"></a>Expiração de cookie absoluto

Um tempo de expiração absoluto pode ser <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>definido com. Para criar um cookie persistente, `IsPersistent` também deve ser definido. Caso contrário, o cookie é criado com um tempo de vida baseado em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém. Quando `ExpiresUtc` é definido, ele substitui o valor <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> da opção de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se definido.

O trecho de código a seguir cria uma identidade e um cookie correspondente que dura 20 minutos. Isso ignora as configurações de expiração deslizante configuradas anteriormente.

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Verificações de função baseadas em política](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
