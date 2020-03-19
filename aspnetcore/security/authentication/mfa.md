---
title: Autenticação multifator no ASP.NET Core
author: damienbod
description: Saiba como configurar a MFA (autenticação multifator) em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Identity
uid: security/authentication/mfa
ms.openlocfilehash: 6220688d53f0718ca5be5f63dd5d9539d37e2391
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520193"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>Autenticação multifator no ASP.NET Core

Por [Damien Bowden](https://github.com/damienbod)

A autenticação multifator (MFA) é um processo no qual um usuário é solicitado durante um evento de entrada para formulários adicionais de identificação. Esse prompt pode ser para inserir um código de um celular, usar uma chave FIDO2 ou para fornecer uma verificação de impressão digital. Quando você precisa de uma segunda forma de autenticação, a segurança é aprimorada. O fator adicional não é facilmente obtido ou duplicado por um invasor.

Este artigo aborda as seguintes áreas:

* O que é MFA e quais fluxos de MFA são recomendados
* Configurar o MFA para páginas de administração usando ASP.NET Core Identity
* Enviar requisito de entrada MFA ao servidor OpenID Connect
* Forçar ASP.NET Core cliente do OpenID Connect a exigir MFA

## <a name="mfa-2fa"></a>MFA, 2FA

A MFA exige pelo menos dois ou mais tipos de provas para uma identidade como algo que você conhece, algo que você possui ou validação biométrica para o usuário se autenticar.

A autenticação de dois fatores (2FA) é como um subconjunto de MFA, mas a diferença é que a MFA pode exigir dois ou mais fatores para provar a identidade.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (algoritmo de senha de uso único baseado em tempo)

O MFA usando TOTP é uma implementação com suporte usando ASP.NET Core Identity. Isso pode ser usado junto com qualquer aplicativo autenticador compatível, incluindo:

* Aplicativo Microsoft Authenticator
* Aplicativo de autenticador do Google

Consulte o link a seguir para obter detalhes de implementação:

[Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>MFA FIDO2 ou com senha

No momento, FIDO2 é:

* A maneira mais segura de obter MFA.
* O único fluxo de MFA que protege contra ataques de phishing.

No momento, ASP.NET Core não dá suporte diretamente a FIDO2. FIDO2 pode ser usado para fluxos MFA ou com senha.

Azure Active Directory fornece suporte para fluxos FIDO2 e sem senha. Para obter mais informações, consulte [Opções de autenticação com senha para Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).

### <a name="mfa-sms"></a>SMS MFA

A MFA com o SMS aumenta a segurança em grande comparação com a autenticação de senha (fator único). No entanto, o uso do SMS como um segundo fator não é mais recomendado. Existem muitos vetores de ataque conhecidos para esse tipo de implementação.

[Diretrizes do NIST](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-opno-locidentity"></a>Configurar o MFA para páginas de administração usando ASP.NET Core Identity

A MFA pode ser forçada para os usuários acessarem páginas confidenciais em um aplicativo ASP.NET Core Identity. Isso pode ser útil para aplicativos em que existem diferentes níveis de acesso para as diferentes identidades. Por exemplo, os usuários podem ser capazes de exibir os dados de perfil usando um logon de senha, mas um administrador precisaria usar o MFA para acessar as páginas administrativas.

### <a name="extend-the-login-with-an-mfa-claim"></a>Estender o logon com uma declaração MFA

O código de demonstração é configurado usando ASP.NET Core com Identity e Razor Pages. O método `AddIdentity` é usado em vez de `AddDefaultIdentity` um, portanto, uma implementação de `IUserClaimsPrincipalFactory` pode ser usada para adicionar declarações à identidade após um logon bem-sucedido.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

A classe `AdditionalUserClaimsPrincipalFactory` adiciona a declaração de `amr` às declarações do usuário somente após um logon bem-sucedido. O valor da declaração é lido no banco de dados. A declaração é adicionada aqui porque o usuário só deve acessar a exibição protegida mais alta se a identidade tiver feito logon com MFA. Se a exibição do banco de dados for lida diretamente do banco de dados em vez de usar a declaração, é possível acessar a exibição sem MFA diretamente após ativar a MFA.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

Como a instalação do serviço de Identity foi alterada na classe `Startup`, os layouts do Identity precisam ser atualizados. Scaffold as páginas de Identity no aplicativo. Defina o layout no arquivo *Identity/Account/Manage/_Layout. cshtml* .

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Também atribua o layout para todas as páginas de gerenciamento das páginas Identity:

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Validar o requisito de MFA na página de administração

A página de administração do Razor valida que o usuário fez logon usando MFA. No método `OnGet`, a identidade é usada para acessar as declarações do usuário. A declaração de `amr` é verificada quanto ao valor `mfa`. Se a identidade não tiver essa declaração ou estiver `false`, a página redirecionará para a página habilitar MFA. Isso é possível porque o usuário já fez logon, mas sem MFA.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>Lógica da interface do usuário para ativar ou desativar informações de logon

Uma política de autorização foi adicionada na inicialização. A política requer a declaração de `amr` com o valor `mfa`.

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Essa política pode ser usada na exibição `_Layout` para mostrar ou ocultar o menu **admin** com o aviso:

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Se a identidade tiver feito logon usando o MFA, o menu **admin** será exibido sem o aviso da dica de ferramenta. Quando o usuário fizer logon sem MFA, o menu **admin (não habilitado)** será exibido junto com a dica de ferramenta que informa ao usuário (explicando o aviso).

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

Se o usuário fizer logon sem MFA, o aviso será exibido:

![Autenticação de MFA do administrador](mfa/_static/identitystandalonemfa_01.png)

O usuário é redirecionado para a exibição habilitar do MFA ao clicar no link do **administrador** :

![O administrador ativa a autenticação MFA](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>Enviar requisito de entrada MFA ao servidor OpenID Connect 

O parâmetro `acr_values` pode ser usado para passar o `mfa` valor necessário do cliente para o servidor em uma solicitação de autenticação.

> [!NOTE]
> O parâmetro `acr_values` precisa ser manipulado no servidor do Open ID Connect para que isso funcione.

### <a name="openid-connect-aspnet-core-client"></a>Cliente do OpenID Connect ASP.NET Core

O ASP.NET Core Razor Pages aplicativo cliente Open ID Connect usa o método `AddOpenIdConnect` para fazer logon no servidor do Open ID Connect. O parâmetro `acr_values` é definido com o valor de `mfa` e enviado com a solicitação de autenticação. O `OpenIdConnectEvents` é usado para adicionar isso.

Para obter `acr_values` valores de parâmetro recomendados, consulte [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-opno-locidentity"></a>Exemplo de servidor OpenID Connect IdentityServer 4 com ASP.NET Core Identity

No servidor OpenID Connect, que é implementado usando ASP.NET Core Identity com exibições MVC, uma nova exibição chamada *ErrorEnable2FA. cshtml* é criada. A exibição:

* Exibe se o Identity vem de um aplicativo que requer MFA, mas o usuário não ativou isso em Identity.
* Informa ao usuário e adiciona um link para ativar isso.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

No método `Login`, a implementação da interface `IIdentityServerInteractionService` `_interaction` é usada para acessar os parâmetros de solicitação do Open ID Connect. O parâmetro `acr_values` é acessado usando a propriedade `AcrValues`. Como o cliente enviou isso com `mfa` definido, isso pode ser verificado.

Se a MFA for necessária e o usuário no ASP.NET Core Identity tiver a MFA habilitada, o logon continuará. Quando o usuário não tem a MFA habilitada, o usuário é redirecionado para a exibição personalizada *ErrorEnable2FA. cshtml*. Em seguida, ASP.NET Core Identity assina o usuário.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

O método `ExternalLoginCallback` funciona como o logon de Identity local. A propriedade `AcrValues` é verificada quanto ao valor de `mfa`. Se o valor de `mfa` estiver presente, a MFA será forçada antes que o logon seja concluído (por exemplo, Redirecionado para a exibição de `ErrorEnable2FA`).

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

Se o usuário já estiver conectado, o aplicativo cliente:

* Ainda valida a declaração de `amr`.
* Pode configurar o MFA com um link para o ASP.NET Core Identity exibição.

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>Forçar ASP.NET Core cliente do OpenID Connect a exigir MFA

Este exemplo mostra como um ASP.NET Core aplicativo de página Razor, que usa o OpenID Connect para entrar, pode exigir que os usuários tenham se autenticado usando o MFA.

Para validar o requisito de MFA, um requisito de `IAuthorizationRequirement` é criado. Isso será adicionado às páginas usando uma política que requer MFA.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

Um `AuthorizationHandler` é implementado para usar a declaração de `amr` e verificar o valor `mfa`. O `amr` é retornado na `id_token` de uma autenticação bem-sucedida e pode ter muitos valores diferentes, conforme definido na especificação de [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

O valor retornado depende de como a identidade é autenticada e na implementação do servidor do Open ID Connect.

O `AuthorizationHandler` usa o requisito de `RequireMfa` e valida a declaração de `amr`. O servidor OpenID Connect pode ser implementado usando o IdentityServer4 com IdentityASP.NET Core. Quando um usuário faz logon usando TOTP, a declaração de `amr` é retornada com um valor de MFA. Se estiver usando uma implementação de servidor OpenID Connect diferente ou um tipo de MFA diferente, a declaração de `amr` será ou poderá ter um valor diferente. O código deve ser estendido para aceitar isso também.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

No método `Startup.ConfigureServices`, o método `AddOpenIdConnect` é usado como o esquema de desafio padrão. O manipulador de autorização, que é usado para verificar a declaração de `amr`, é adicionado à inversão do contêiner de controle. Em seguida, uma política é criada, o que adiciona o requisito de `RequireMfa`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

Essa política é usada na página Razor conforme necessário. A política também pode ser adicionada globalmente para todo o aplicativo.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

Se o usuário se autenticar sem MFA, a declaração de `amr` provavelmente terá um valor de `pwd`. A solicitação não será autorizada a acessar a página. Usando os valores padrão, o usuário será redirecionado para a página *Account/AccessDenied* . Esse comportamento pode ser alterado ou você pode implementar sua própria lógica personalizada aqui. Neste exemplo, um link é adicionado para que o usuário válido possa configurar o MFA para sua conta.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

Agora, somente os usuários que se autenticam com o MFA podem acessar a página ou o site. Se tipos de MFA diferentes forem usados ou se 2FA estiver ok, a declaração de `amr` terá valores diferentes e precisará ser processada corretamente. Os diferentes servidores do Open ID Connect também retornam valores diferentes para essa declaração e podem não seguir a especificação de [valores de referência do método de autenticação](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Ao fazer logon sem MFA (por exemplo, usando apenas uma senha):

* O `amr` tem o valor de `pwd`:

    ![require_mfa_oidc_02. png](mfa/_static/require_mfa_oidc_02.png)

* Acesso negado:

    ![require_mfa_oidc_03. png](mfa/_static/require_mfa_oidc_03.png)

Como alternativa, faça logon usando OTP com Identity:

![require_mfa_oidc_01. png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Habilitar geração de código QR para aplicativos do autenticador TOTP no ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Opções de autenticação com senha para Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless)
* [FIDO2 biblioteca .NET para FIDO2/webauthn atestado e asserção usando o .NET](https://github.com/abergs/fido2-net-lib)
* [Webauthn awesome](https://github.com/herrjemand/awesome-webauthn)
