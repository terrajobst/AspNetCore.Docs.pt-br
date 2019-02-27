---
title: Usar autenticação de cookie sem o ASP.NET Core Identity
author: rick-anderson
description: Obter uma explicação de como usar a autenticação de cookie sem o ASP.NET Core Identity
ms.author: riande
ms.date: 02/25/2019
uid: security/authentication/cookie
ms.openlocfilehash: 29370a3ff25469b34edc2a71e00601cf6ecc00ca
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899276"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Usar autenticação de cookie sem o ASP.NET Core Identity

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)

Como você viu nos tópicos anteriores de autenticação, [ASP.NET Core Identity](xref:security/authentication/identity) é um provedor de autenticação completa e a versão completa para criar e manter os logons. No entanto, você talvez queira usar sua própria lógica de autenticação personalizada com autenticação baseada em cookie às vezes. Você pode usar a autenticação baseada em cookie como um provedor de autenticação autônomo sem o ASP.NET Core Identity.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([como baixar](xref:index#how-to-download-a-sample))

Para fins de demonstração no aplicativo de exemplo, a conta de usuário para o usuário hipotético, Maria Rodriguez, é codificados no aplicativo. Use o nome de usuário de Email "maria.rodriguez@contoso.com" e nenhuma senha para a entrada do usuário. O usuário é autenticado na `AuthenticateUser` método na *Pages/Account/Login.cshtml.cs* arquivo. Em um exemplo do mundo real, o usuário deve ser autenticado em relação a um banco de dados.

Para obter informações sobre a autenticação baseada em cookie Migrando do ASP.NET Core 1.x para 2.0, consulte [migrar autenticação e identidade para o tópico do ASP.NET Core 2.0 (autenticação baseada em Cookie)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).

Para usar a identidade do ASP.NET Core, consulte o [Introdução à identidade](xref:security/authentication/identity) tópico.

## <a name="configuration"></a>Configuração

::: moniker range=">= aspnetcore-2.0"

Se o aplicativo não usa o [metapacote do Microsoft](xref:fundamentals/metapackage-app), criar uma referência de pacote no arquivo de projeto para o [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) pacote (versão 2.1.0 ou mais tarde).

No `ConfigureServices` método, crie o serviço de Middleware de autenticação com o `AddAuthentication` e `AddCookie` métodos:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

`AuthenticationScheme` passado para `AddAuthentication` define o esquema de autenticação padrão para o aplicativo. `AuthenticationScheme` é útil quando há várias instâncias de autenticação de cookie e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme). Definindo o `AuthenticationScheme` para `CookieAuthenticationDefaults.AuthenticationScheme` fornece um valor de "Cookies" para o esquema. Você pode fornecer qualquer valor de cadeia de caracteres que diferencia o esquema.

Esquema de autenticação do aplicativo é diferente do esquema de autenticação de cookie do aplicativo. Quando um esquema de autenticação de cookie não é fornecido para <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, ele usa [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").

O cookie de autenticação <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> estiver definida como `true` por padrão. Cookies de autenticação são permitidos quando um visitante do site não tiver consentido para coleta de dados. Para obter mais informações, consulte <xref:security/gdpr#essential-cookies>.

No `Configure` método, use o `UseAuthentication` método para invocar o Middleware de autenticação define o `HttpContext.User` propriedade. Chame o `UseAuthentication` método antes de chamar `UseMvcWithDefaultRoute` ou `UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

**Opções de AddCookie**

O [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) classe é usada para configurar as opções de provedor de autenticação.

| Opção | Descrição |
| ------ | ----------- |
| [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | Fornece o caminho para fornecer a uma 302 não encontrado (redirecionamento de URL) quando disparado por `HttpContext.ForbidAsync`. O valor padrão é `/Account/AccessDenied`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | O emissor a ser usado para o [emissor](/dotnet/api/system.security.claims.claim.issuer) propriedade em quaisquer declarações criadas pelo serviço de autenticação de cookie. |
| [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | O nome de domínio no qual o cookie é atendido. Por padrão, isso é o nome do host da solicitação. O navegador envia apenas o cookie em solicitações para um nome de host correspondente. Talvez você queira ajustar isso para ter cookies disponíveis para qualquer host no seu domínio. Por exemplo, definir o domínio do cookie `.contoso.com` disponibiliza para `contoso.com`, `www.contoso.com`, e `staging.www.contoso.com`. |
| [Cookie.HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | Um sinalizador que indica se o cookie deve ser acessível somente aos servidores. A alteração desse valor para `false` permite que os scripts do lado do cliente para acessar o cookie e pode abrir seu aplicativo ao roubo de cookie deve ter de seu aplicativo uma [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerabilidade. O valor padrão é `true`. |
| [Cookie.Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | Define o nome do cookie. |
| [Cookie.Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | Usado para isolar aplicativos em execução no mesmo nome de host. Se você tiver um aplicativo em execução no `/app1` e para restringir os cookies para o aplicativo, defina a `CookiePath` propriedade `/app1`. Ao fazer isso, o cookie só está disponível em solicitações para `/app1` e qualquer aplicativo abaixo dela. |
| [Cookie.SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | Indica se o navegador deve permitir que o cookie a ser anexado a somente solicitações do mesmo site (`SameSiteMode.Strict`) ou solicitações entre sites usando métodos seguros de HTTP e as solicitações do mesmo site (`SameSiteMode.Lax`). Quando definido como `SameSiteMode.None`, o valor do cabeçalho de cookie não está definido. Observe que [Middleware do Cookie política](#cookie-policy-middleware) pode substituir o valor fornecido por você. Para dar suporte à autenticação OAuth, o valor padrão é `SameSiteMode.Lax`. Para obter mais informações, consulte [autenticação OAuth interrompida devido à política de cookies SameSite](https://github.com/aspnet/Security/issues/1231). |
| [Cookie.SecurePolicy](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | Um sinalizador que indica se o cookie criado deve ser limitado a HTTPS (`CookieSecurePolicy.Always`), HTTP ou HTTPS (`CookieSecurePolicy.None`), ou o mesmo protocolo usado na solicitação (`CookieSecurePolicy.SameAsRequest`). O valor padrão é `CookieSecurePolicy.SameAsRequest`. |
| [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | Define o `DataProtectionProvider` que é usado para criar o padrão `TicketDataFormat`. Se o `TicketDataFormat` propriedade for definida, o `DataProtectionProvider` opção não é usada. Se não for fornecido, o provedor de proteção de dados do aplicativo padrão é usado. |
| [Eventos](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | O manipulador chama métodos no provedor que fornecem o controle de aplicativo em determinados pontos de processamento. Se `Events` não são fornecidas, uma instância padrão é fornecida que não faz nada quando os métodos são chamados. |
| [EventsType](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | Usado como o tipo de serviço para obter o `Events` instância em vez da propriedade. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | O `TimeSpan` depois que o tíquete de autenticação armazenado dentro do cookie expira. `ExpireTimeSpan` é adicionado à hora atual para criar o tempo de expiração para o tíquete. O `ExpiredTimeSpan` valor sempre entra no AuthTicket criptografado verificado pelo servidor. Isso também pode acontecer na [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) cabeçalho, mas somente se `IsPersistent` está definido. Para definir `IsPersistent` à `true`, configure o [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passado para `SignInAsync`. O valor padrão de `ExpireTimeSpan` é de 14 dias. |
| [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | Fornece o caminho para fornecer a uma 302 não encontrado (redirecionamento de URL) quando disparado por `HttpContext.ChallengeAsync`. A URL atual que gerou o 401 é adicionada para o `LoginPath` como um parâmetro de cadeia de caracteres de consulta nomeado pelo `ReturnUrlParameter`. Uma vez uma solicitação para o `LoginPath` concede uma nova entrada identidade, o `ReturnUrlParameter` valor é usado para redirecionar o navegador para a URL que causou o código de status não autorizado original. O valor padrão é `/Account/Login`. |
| [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | Se o `LogoutPath` é fornecido para o manipulador, em seguida, redireciona uma solicitação para o caminho com base no valor da `ReturnUrlParameter`. O valor padrão é `/Account/Logout`. |
| [ReturnUrlParameter](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | Determina o nome do parâmetro de cadeia de consulta que é acrescentado pelo manipulador para uma resposta 302 do encontrado (redirecionamento de URL). `ReturnUrlParameter` é usado quando uma solicitação chega na `LoginPath` ou `LogoutPath` para retornar o navegador a URL original depois que a ação de logon ou logoff é executada. O valor padrão é `ReturnUrl`. |
| [SessionStore](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | Um contêiner opcional usado para armazenar a identidade entre solicitações. Quando usado, somente um identificador de sessão é enviado ao cliente. `SessionStore` pode ser usado para atenuar problemas potenciais com identidades grandes. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | Um sinalizador que indica se um novo cookie com um tempo de expiração atualizados deve ser emitido dinamicamente. Isso pode acontecer em qualquer solicitação em que o período de expiração do cookie atual mais de 50% expirou. A nova data de expiração é movida para frente até ser a data atual mais o `ExpireTimespan`. Uma [tempo de expiração do cookie absoluto](xref:security/authentication/cookie#absolute-cookie-expiration) pode ser definida usando o `AuthenticationProperties` classe ao chamar `SignInAsync`. Um tempo de expiração absoluto pode melhorar a segurança do seu aplicativo, limitando a quantidade de tempo que o cookie de autenticação é válido. O valor padrão é `true`. |
| [TicketDataFormat](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | O `TicketDataFormat` é usado para proteger e desproteger a identidade e outras propriedades que são armazenadas no valor do cookie. Se não for fornecido, um `TicketDataFormat` é criado usando o [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0). |
| [Validar](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | Método que verifica que as opções são válidas. |

Definir `CookieAuthenticationOptions` na configuração do serviço para autenticação no `ConfigureServices` método:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

ASP.NET Core 1.x usa cookie [middleware](xref:fundamentals/middleware/index) que serializa uma entidade de usuário em um cookie criptografado. Em solicitações subsequentes, o cookie é validado, e a entidade de segurança é recriada e atribuída ao `HttpContext.User` propriedade.

Instalar o [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) pacote do NuGet em seu projeto. Este pacote contém o cookie de middleware.

Use o `UseCookieAuthentication` método no `Configure` método na sua *Startup.cs* antes do arquivo `UseMvc` ou `UseMvcWithDefaultRoute`:

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

**Opções de CookieAuthenticationOptions**

O [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) classe é usada para configurar as opções de provedor de autenticação.

| Opção | Descrição |
| ------ | ----------- |
| [AuthenticationScheme](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | Define o esquema de autenticação. `AuthenticationScheme` é útil quando há várias instâncias de autenticação e você deseja [autorizar com um esquema específico](xref:security/authorization/limitingidentitybyscheme). Definindo o `AuthenticationScheme` para `CookieAuthenticationDefaults.AuthenticationScheme` fornece um valor de "Cookies" para o esquema. Você pode fornecer qualquer valor de cadeia de caracteres que diferencia o esquema. |
| [AutomaticAuthenticate](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | Define um valor para indicar que a autenticação de cookie deve executar em cada solicitação e tente validar e reconstrua qualquer entidade de segurança serializada criado por ele. |
| [AutomaticChallenge](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | Se for true, o middleware de autenticação lida com desafios automática. Se false, o middleware de autenticação altera somente as respostas quando explicitamente indicado pelo `AuthenticationScheme`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | O emissor a ser usado para o [emissor](/dotnet/api/system.security.claims.claim.issuer) propriedade em quaisquer declarações criado pelo middleware de autenticação de cookie. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | O nome de domínio no qual o cookie é atendido. Por padrão, isso é o nome do host da solicitação. O navegador só serve o cookie para um nome de host correspondente. Talvez você queira ajustar isso para ter cookies disponíveis para qualquer host no seu domínio. Por exemplo, definir o domínio do cookie `.contoso.com` disponibiliza para `contoso.com`, `www.contoso.com`, e `staging.www.contoso.com`. |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | Um sinalizador que indica se o cookie deve ser acessível somente aos servidores. A alteração desse valor para `false` permite que os scripts do lado do cliente para acessar o cookie e pode abrir seu aplicativo ao roubo de cookie deve ter de seu aplicativo uma [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerabilidade. O valor padrão é `true`. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | Usado para isolar aplicativos em execução no mesmo nome de host. Se você tiver um aplicativo em execução no `/app1` e para restringir os cookies para o aplicativo, defina a `CookiePath` propriedade `/app1`. Ao fazer isso, o cookie só está disponível em solicitações para `/app1` e qualquer aplicativo abaixo dela. |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | Um sinalizador que indica se o cookie criado deve ser limitado a HTTPS (`CookieSecurePolicy.Always`), HTTP ou HTTPS (`CookieSecurePolicy.None`), ou o mesmo protocolo usado na solicitação (`CookieSecurePolicy.SameAsRequest`). O valor padrão é `CookieSecurePolicy.SameAsRequest`. |
| [Descrição](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | Informações adicionais sobre o tipo de autenticação que será disponibilizado para o aplicativo. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | O `TimeSpan` depois que o tíquete de autenticação expira. Ele é adicionado à hora atual para criar o tempo de expiração para o tíquete. Para usar `ExpireTimeSpan`, você deve definir `IsPersistent` ao `true` no `AuthenticationProperties` passado para `SignInAsync`. O valor padrão é 14 dias. |
| [SlidingExpiration](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | Um sinalizador que indica se a data de expiração do cookie redefine quando houver mais de metade do `ExpireTimeSpan` intervalo tenha decorrido. A nova hora exipiration é movida para frente até ser a data atual mais o `ExpireTimespan`. Uma [tempo de expiração do cookie absoluto](xref:security/authentication/cookie#absolute-cookie-expiration) pode ser definida usando o `AuthenticationProperties` classe ao chamar `SignInAsync`. Um tempo de expiração absoluto pode melhorar a segurança do seu aplicativo, limitando a quantidade de tempo que o cookie de autenticação é válido. O valor padrão é `true`. |

Definir `CookieAuthenticationOptions` para o Middleware de autenticação de Cookie no `Configure` método:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a>Cookie de Middleware de política

[Middleware do cookie política](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) habilita recursos de política de cookie em um aplicativo. Adicionar o middleware ao pipeline de processamento de aplicativos é a ordem de minúsculas; ela afeta apenas os componentes registrados depois no pipeline.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 O [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) fornecido para o Cookie de Middleware de política permitem controlar características globais do processamento de cookie e gancho em manipuladores de processamento do cookie quando os cookies são acrescentados ou excluídos.

| Propriedade | Descrição |
| -------- | ----------- |
| [HttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | Afeta se os cookies devem ser HttpOnly, que é um sinalizador que indica se o cookie deve ser acessível somente aos servidores. O valor padrão é `HttpOnlyPolicy.None`. |
| [MinimumSameSitePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | Afeta o atributo de mesmo site do cookie (veja abaixo). O valor padrão é `SameSiteMode.Lax`. Essa opção está disponível para o ASP.NET Core 2.0 +. |
| [OnAppendCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | Chamado quando um cookie será acrescentado. |
| [OnDeleteCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | Chamado quando um cookie é excluído. |
| [Proteger](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | Afeta se os cookies devem ser seguro. O valor padrão é `CookieSecurePolicy.None`. |

**MinimumSameSitePolicy** (ASP.NET Core 2.0 ou posterior somente)

O padrão `MinimumSameSitePolicy` valor é `SameSiteMode.Lax` para permitir a autenticação OAuth2. Estritamente impor uma política do mesmo site do `SameSiteMode.Strict`, defina o `MinimumSameSitePolicy`. Embora essa configuração interrompe OAuth2 e outras esquemas de autenticação entre origens, ela eleva o nível de segurança do cookie para outros tipos de aplicativos que não dependem de processamento de solicitação entre origens.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

A configuração de Middleware de política de Cookie para `MinimumSameSitePolicy` pode afetar sua configuração de `Cookie.SameSite` em `CookieAuthenticationOptions` configurações de acordo com a matriz a seguir.

| MinimumSameSitePolicy | Cookie.SameSite | Configuração de Cookie.SameSite resultante |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Criar um cookie de autenticação

Para criar um cookie contendo informações de usuário, você precisa construir uma [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). As informações do usuário são serializadas e armazenadas no cookie. 

::: moniker range=">= aspnetcore-2.0"

Criar uma [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) com qualquer necessárias [declaração](/dotnet/api/system.security.claims.claim)s e chame [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) para a entrada do usuário:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Chame [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) para a entrada do usuário:

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

`SignInAsync` cria um cookie criptografado e o adiciona à resposta atual. Se você não especificar um `AuthenticationScheme`, o esquema padrão é usado.

Nos bastidores, a criptografia usada é ASP.NET Core [proteção de dados](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) sistema. Se você estiver hospedando o aplicativo em várias máquinas, balanceamento de carga entre aplicativos ou usar uma web farm, você deve [configurar a proteção de dados](xref:security/data-protection/configuration/overview) para usar o mesmo anel de chave e o identificador do aplicativo.

## <a name="sign-out"></a>Sair

::: moniker range=">= aspnetcore-2.0"

Para desconectar o usuário atual e excluir seus cookies, chame [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Para desconectar o usuário atual e excluir seus cookies, chame [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

Se você não estiver usando `CookieAuthenticationDefaults.AuthenticationScheme` (ou "Cookies") como o esquema (por exemplo, "ContosoCookie"), forneça o esquema usado ao configurar o provedor de autenticação. Caso contrário, o esquema padrão é usado.

## <a name="react-to-back-end-changes"></a>Reagir às alterações de back-end

Depois que um cookie é criado, ele se torna a única fonte de identidade. Mesmo se você desabilitar um usuário em seus sistemas de back-end, o sistema de autenticação de cookie não tem conhecimento disso, e um usuário permaneça conectado enquanto seu cookie é válido.

O [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) evento no ASP.NET Core 2.x ou o [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) método no ASP.NET Core 1.x pode ser usado para interceptar e substituir a validação da identidade do cookie. Essa abordagem minimiza o risco de usuários revogados acessando o aplicativo.

Uma abordagem de validação de cookie baseia-se em manter o controle de quando o banco de dados do usuário foi alterado. Se o banco de dados não foi alterado desde que o cookie do usuário foi emitido, não é necessário para autenticar o usuário novamente se o cookie ainda é válido. Para implementar este cenário, o banco de dados, que é implementado no `IUserRepository` para este exemplo armazena um `LastChanged` valor. Quando nenhum usuário é atualizado no banco de dados, o `LastChanged` valor é definido como a hora atual.

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

::: moniker range=">= aspnetcore-2.0"

Para implementar uma substituição para o `ValidatePrincipal` eventos, escreva um método com a assinatura a seguir em uma classe que você deriva [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Um exemplo é semelhante ao seguinte:

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

Registrar a instância de eventos durante o registro do serviço de cookie no `ConfigureServices` método. Fornecer um registro de serviço com escopo para sua `CustomCookieAuthenticationEvents` classe:

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

Para implementar uma substituição para o `ValidateAsync` eventos, escreva um método com a seguinte assinatura:

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

Identidade do ASP.NET Core implementa essa verificação como parte de sua [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync). Um exemplo é semelhante ao seguinte:

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

Registrar o evento durante a configuração de autenticação de cookie no `Configure` método:

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

Considere uma situação em que o nome do usuário é atualizado &mdash; uma decisão que não afeta a segurança de qualquer forma. Se você quiser atualizar forma não destrutiva a entidade de usuário, chame `context.ReplacePrincipal` e defina o `context.ShouldRenew` propriedade `true`.

> [!WARNING]
> A abordagem descrita aqui é disparada em cada solicitação. Isso pode resultar em uma penalidade de desempenho grande para o aplicativo.

## <a name="persistent-cookies"></a>Cookies persistentes

Talvez você queira que o cookie para persistir entre as sessões do navegador. Essa persistência deve ser habilitada apenas com o consentimento explícito do usuário com uma caixa de seleção "Lembrar-Me" no logon ou um mecanismo semelhante. 

O trecho de código a seguir cria uma identidade e o cookie correspondente que sobrevive a por meio de fechamentos de navegador. Quaisquer configurações de expiração deslizante configuradas anteriormente são consideradas. Se o cookie expirar enquanto o navegador é fechado, o navegador limpa o cookie depois que ele seja reiniciado.

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

O [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) classe reside no `Microsoft.AspNetCore.Authentication` namespace.

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

O [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) classe reside no `Microsoft.AspNetCore.Http.Authentication` namespace.

::: moniker-end

## <a name="absolute-cookie-expiration"></a>Expiração do cookie absoluto

Você pode definir um tempo de expiração absoluto com `ExpiresUtc`. Para criar um cookie persistente, você também deve definir `IsPersistent`; caso contrário, o cookie é criado com um tempo de vida com base em sessão e pode expirar antes ou depois que a autenticação de tíquete que ele contém. Quando `ExpiresUtc` é definida em `SignInAsync`, ele substitui o valor da `ExpireTimeSpan` opção de `CookieAuthenticationOptions`, se definido.

O trecho de código a seguir cria uma identidade e o cookie correspondente que dura por 20 minutos. Isso ignora as configurações de expiração deslizante configuradas anteriormente.

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

## <a name="additional-resources"></a>Recursos adicionais

* [As alterações do AUTH 2.0 / migração comunicado](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Verificações de função baseado em políticas](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
