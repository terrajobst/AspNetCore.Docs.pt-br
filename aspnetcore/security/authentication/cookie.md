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
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Usar autenticação de cookie sem o ASP.NET Core Identity

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)

ASP.NET Core Identity é um provedor de autenticação completa e a versão completa para criar e manter os logons. No entanto, um provedor de autenticação da autenticação baseada em cookie sem o ASP.NET Core Identity pode ser usado. Para obter mais informações, consulte <xref:security/authentication/identity>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))

Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificados no aplicativo. Use o **E-mail** nome de usuário `maria.rodriguez@contoso.com` e nenhuma senha para a entrada do usuário. O usuário é autenticado na `AuthenticateUser` método na *Pages/Account/Login.cshtml.cs* arquivo. Em um exemplo do mundo real, o usuário deve ser autenticado em relação a um banco de dados.

## <a name="configuration"></a>Configuração

Se o aplicativo não usa o [metapacote do Microsoft](xref:fundamentals/metapackage-app), criar uma referência de pacote no arquivo de projeto para o [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) pacote.

No `Startup.ConfigureServices` método, crie o serviço de Middleware de autenticação com o <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> métodos:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo. `AuthenticationScheme` é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme). Definindo o `AuthenticationScheme` à [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) fornece um valor de "Cookies" para o esquema. Você pode fornecer qualquer valor de cadeia de caracteres que diferencia o esquema.

Esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo. Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, ele usa `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").

O cookie de autenticação <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> estiver definida como `true` por padrão. Cookies de autenticação são permitidos quando um visitante do site não tiver consentido para coleta de dados. Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.

No `Startup.Configure` método, a chamada a `UseAuthentication` método para invocar o Middleware de autenticação define o `HttpContext.User` propriedade. Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> classe é usada para configurar as opções de provedor de autenticação.

Definir `CookieAuthenticationOptions` na configuração do serviço para autenticação no `Startup.ConfigureServices` método:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Cookie de Middleware de política

[Middleware do cookie política](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) habilita recursos de política de cookie. Adicionar o middleware ao pipeline de processamento de aplicativos é a ordem confidencial&mdash;ele afeta somente os componentes downstream, registrados no pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> fornecido para o Cookie de Middleware de política para controlar características globais do processamento de cookie e gancho em manipuladores de processamento do cookie quando os cookies são acrescentados ou excluídos.

O padrão <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> valor é `SameSiteMode.Lax` para permitir a autenticação OAuth2. Estritamente impor uma política do mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`. Embora essa configuração interrompe OAuth2 e outras esquemas de autenticação entre origens, ela eleva o nível de segurança do cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

A configuração de Middleware de política de Cookie para `MinimumSameSitePolicy` podem afetar a definição de `Cookie.SameSite` em `CookieAuthenticationOptions` configurações de acordo com a matriz a seguir.

| MinimumSameSitePolicy | Cookie.SameSite | Configuração de Cookie.SameSite resultante |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Criar um cookie de autenticação

Para criar um cookie contendo informações de usuário, construir um <xref:System.Security.Claims.ClaimsPrincipal>. As informações do usuário são serializadas e armazenadas no cookie. 

Criar uma <xref:System.Security.Claims.ClaimsIdentity> com qualquer necessária <xref:System.Security.Claims.Claim>s e chamada <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> para a entrada do usuário:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` cria um cookie criptografado e o adiciona à resposta atual. Se `AuthenticationScheme` não for especificado, o esquema padrão é usado.

ASP.NET Core [proteção de dados](xref:security/data-protection/using-data-protection) sistema é usado para criptografia. Para um aplicativo hospedado em várias máquinas, carregar balanceamento entre aplicativos, ou usando uma web farm [configurar a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo anel de chave e o identificador do aplicativo.

## <a name="sign-out"></a>Sair

Para desconectar o usuário atual e excluir seus cookies, chamar <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Se `CookieAuthenticationDefaults.AuthenticationScheme` (ou "Cookies") não é usado como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação. Caso contrário, o esquema padrão é usado.

## <a name="react-to-back-end-changes"></a>Reagir às alterações de back-end

Depois que um cookie é criado, o cookie é a única fonte de identidade. Se uma conta de usuário é desabilitada nos sistemas de back-end:

* Sistema de autenticação de cookie do aplicativo continua a processar solicitações com base no cookie de autenticação.
* O usuário permanece conectado ao aplicativo, desde que o cookie de autenticação é válido.

O <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> evento pode ser usado para interceptar e substituir a validação da identidade do cookie. Validar o cookie em cada solicitação minimiza o risco de usuários revogados acessando o aplicativo.

Uma abordagem de validação de cookie baseia-se em manter o controle de quando o banco de dados do usuário é alterado. Se o banco de dados não foi alterado desde que o cookie do usuário foi emitido, não é necessário para autenticar o usuário novamente se o cookie ainda é válido. No aplicativo de exemplo, o banco de dados é implementado no `IUserRepository` e armazena um `LastChanged` valor. Quando um usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.

Para invalidar um cookie, quando as alterações de banco de dados com base nas `LastChanged` o valor, criar o cookie com um `LastChanged` contendo atual de declaração `LastChanged` valor do banco de dados:

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

Para implementar uma substituição para o `ValidatePrincipal` eventos, escreva um método com a assinatura a seguir em uma classe que deriva de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

A seguir está um exemplo de implementação de `CookieAuthenticationEvents`:

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

Registrar a instância de eventos durante o registro do serviço de cookie no `Startup.ConfigureServices` método. Fornecer um [com escopo de registro do serviço](xref:fundamentals/dependency-injection#service-lifetimes) para seu `CustomCookieAuthenticationEvents` classe:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Considere uma situação em que o nome do usuário é atualizado&mdash;uma decisão que não afeta a segurança de qualquer forma. Se você quiser atualizar forma não destrutiva a entidade de usuário, chame `context.ReplacePrincipal` e defina o `context.ShouldRenew` propriedade `true`.

> [!WARNING]
> A abordagem descrita aqui é disparada em cada solicitação. Validar os cookies de autenticação para todos os usuários em cada solicitação pode resultar em uma penalidade de desempenho grande para o aplicativo.

## <a name="persistent-cookies"></a>Cookies persistentes

Talvez você queira que o cookie para persistir entre as sessões do navegador. Essa persistência deve ser habilitada apenas com o consentimento explícito do usuário com uma caixa de seleção "Lembrar-Me" na entrada ou um mecanismo semelhante. 

O trecho de código a seguir cria uma identidade e o cookie correspondente que sobrevive a por meio de fechamentos de navegador. Quaisquer configurações de expiração deslizante configuradas anteriormente são consideradas. Se o cookie expirar enquanto o navegador é fechado, o navegador limpa o cookie depois que ele seja reiniciado.

Definir <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> à `true` em <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

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

## <a name="absolute-cookie-expiration"></a>Expiração do cookie absoluto

Um tempo de expiração absoluto pode ser definido com <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>. Para criar um cookie persistente, `IsPersistent` também deve ser definido. Caso contrário, o cookie é criado com um tempo de vida com base em sessão e pode expirar antes ou depois do tíquete de autenticação que ele contém. Quando `ExpiresUtc` estiver definido, ele substitui o valor a <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> opção de <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, se definido.

O trecho de código a seguir cria uma identidade e o cookie correspondente que dura por 20 minutos. Isso ignora as configurações de expiração deslizante configuradas anteriormente.

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Verificações de função baseado em políticas](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
