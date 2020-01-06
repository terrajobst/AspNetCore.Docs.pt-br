---
title: Migrar autenticação e identidade para o ASP.NET Core 2,0
author: scottaddie
description: Este artigo descreve as etapas mais comuns para migrar a identidade e a autenticação do ASP.NET Core 1. x para o ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: f3817fa1808c331f7e167618e3bb00d68ad08571
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355172"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Migrar autenticação e identidade para o ASP.NET Core 2,0

Por [Scott Addie](https://github.com/scottaddie) e [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2,0 tem um novo modelo para autenticação e [identidade](xref:security/authentication/identity) que simplifica a configuração usando serviços. ASP.NET Core aplicativos 1. x que usam autenticação ou identidade podem ser atualizados para usar o novo modelo, conforme descrito abaixo.

## <a name="update-namespaces"></a>Atualizar namespaces

Em 1. x, as classes como `IdentityRole` e `IdentityUser` foram encontradas no namespace `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

Em 2,0, o namespace <xref:Microsoft.AspNetCore.Identity> se tornou a nova casa para várias dessas classes. Com o código de identidade padrão, as classes afetadas incluem `ApplicationUser` e `Startup`. Ajuste suas instruções de `using` para resolver as referências afetadas.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Middleware e serviços de autenticação

Em projetos 1. x, a autenticação é configurada via middleware. Um método de middleware é invocado para cada esquema de autenticação ao qual você deseja dar suporte.

O exemplo 1. x a seguir configura a autenticação do Facebook com identidade em *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

Em 2,0 projetos, a autenticação é configurada por meio de serviços. Cada esquema de autenticação é registrado no método de `ConfigureServices` de *Startup.cs*. O método `UseIdentity` é substituído por `UseAuthentication`.

O exemplo 2,0 a seguir configura a autenticação do Facebook com identidade em *Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

O método `UseAuthentication` adiciona um único componente de middleware de autenticação, que é responsável pela autenticação automática e pela manipulação de solicitações de autenticação remota. Ele substitui todos os componentes individuais do middleware por um único componente de middleware comum.

Abaixo estão 2,0 instruções de migração para cada esquema de autenticação principal.

### <a name="cookie-based-authentication"></a>Autenticação baseada em cookie

Selecione uma das duas opções abaixo e faça as alterações necessárias em *Startup.cs*:

1. Usar cookies com identidade
    - Substitua `UseIdentity` por `UseAuthentication` no método `Configure`:

        ```csharp
        app.UseAuthentication();
        ```

    - Invoque o método `AddIdentity` no método `ConfigureServices` para adicionar os serviços de autenticação de cookie.
    - Opcionalmente, invoque o `ConfigureApplicationCookie` ou o método `ConfigureExternalCookie` no método `ConfigureServices` para ajustar as configurações de cookie de identidade.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Usar cookies sem identidade
    - Substitua a chamada do método `UseCookieAuthentication` no método `Configure` por `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - Invoque os métodos `AddAuthentication` e `AddCookie` no método `ConfigureServices`:

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>Autenticação de portador JWT

Faça as seguintes alterações no *Startup.cs*:
- Substitua a chamada do método `UseJwtBearerAuthentication` no método `Configure` por `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invoque o método `AddJwtBearer` no método `ConfigureServices`:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Esse trecho de código não usa identidade, portanto, o esquema padrão deve ser definido passando `JwtBearerDefaults.AuthenticationScheme` para o método `AddAuthentication`.

### <a name="openid-connect-oidc-authentication"></a>Autenticação do OpenID Connect (OIDC)

Faça as seguintes alterações no *Startup.cs*:

- Substitua a chamada do método `UseOpenIdConnectAuthentication` no método `Configure` por `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invoque o método `AddOpenIdConnect` no método `ConfigureServices`:

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- Substitua a propriedade `PostLogoutRedirectUri` na ação `OpenIdConnectOptions` com `SignedOutRedirectUri`:

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Autenticação do Facebook

Faça as seguintes alterações no *Startup.cs*:
- Substitua a chamada do método `UseFacebookAuthentication` no método `Configure` por `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invoque o método `AddFacebook` no método `ConfigureServices`:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Autenticação do Google

Faça as seguintes alterações no *Startup.cs*:
- Substitua a chamada do método `UseGoogleAuthentication` no método `Configure` por `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invoque o método `AddGoogle` no método `ConfigureServices`:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Autenticação de conta da Microsoft

Para obter mais informações sobre a autenticação conta Microsoft, consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/14455).

Faça as seguintes alterações no *Startup.cs*:
- Substitua a chamada do método `UseMicrosoftAccountAuthentication` no método `Configure` por `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invoque o método `AddMicrosoftAccount` no método `ConfigureServices`:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Autenticação do Twitter

Faça as seguintes alterações no *Startup.cs*:
- Substitua a chamada do método `UseTwitterAuthentication` no método `Configure` por `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invoque o método `AddTwitter` no método `ConfigureServices`:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Configurando esquemas de autenticação padrão

Em 1. x, as propriedades `AutomaticAuthenticate` e `AutomaticChallenge` da classe base [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) devem ser definidas em um único esquema de autenticação. Não havia uma boa maneira de impor isso.

Em 2,0, essas duas propriedades foram removidas como propriedades na instância de `AuthenticationOptions` individual. Eles podem ser configurados na chamada do método `AddAuthentication` dentro do método `ConfigureServices` de *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

No trecho de código anterior, o esquema padrão é definido como `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").

Como alternativa, use uma versão sobrecarregada do método `AddAuthentication` para definir mais de uma propriedade. No exemplo de método sobrecarregado a seguir, o esquema padrão é definido como `CookieAuthenticationDefaults.AuthenticationScheme`. O esquema de autenticação pode ser especificado como alternativa em seus atributos de `[Authorize]` individuais ou políticas de autorização.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Defina um esquema padrão em 2,0 se uma das seguintes condições for verdadeira:
- Você deseja que o usuário seja conectado automaticamente
- Você usa o atributo `[Authorize]` ou políticas de autorização sem especificar esquemas

Uma exceção a essa regra é o método `AddIdentity`. Esse método adiciona cookies para você e define os esquemas de autenticação e desafio padrão para o cookie de aplicativo `IdentityConstants.ApplicationScheme`. Além disso, ele define o esquema de entrada padrão para o cookie externo `IdentityConstants.ExternalScheme`.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Usar extensões de autenticação do HttpContext

A interface `IAuthenticationManager` é o ponto de entrada principal no sistema de autenticação 1. x. Ele foi substituído por um novo conjunto de métodos de extensão de `HttpContext` no namespace `Microsoft.AspNetCore.Authentication`.

Por exemplo, os projetos 1. x fazem referência a uma propriedade `Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Em 2,0 projetos, importe o namespace `Microsoft.AspNetCore.Authentication` e exclua as referências de propriedade `Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Autenticação do Windows (HTTP. sys/IISIntegration)

Há duas variações de autenticação do Windows:

* O host só permite usuários autenticados. Essa variação não é afetada pelas alterações 2,0.
* O host permite usuários anônimos e autenticados. Essa variação é afetada pelas alterações 2,0. Por exemplo, o aplicativo deve permitir usuários anônimos na camada [IIS](xref:host-and-deploy/iis/index) ou [http. sys](xref:fundamentals/servers/httpsys) , mas autorizar os usuários no nível do controlador. Nesse cenário, defina o esquema padrão no método `Startup.ConfigureServices`.

  Para [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), defina o esquema padrão como `IISDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Para [Microsoft. AspNetCore. Server. httpsa](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), defina o esquema padrão como `HttpSysDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Falha ao definir o esquema padrão impede que a solicitação de autorização (desafio) funcione com a seguinte exceção:

  > `System.InvalidOperationException`: nenhum authenticationScheme foi especificado e nenhum DefaultChallengeScheme foi encontrado.

Para obter mais informações, consulte <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Instâncias de IdentityCookieOptions

Um efeito colateral das alterações 2,0 é a opção de usar opções nomeadas em vez de instâncias de opções de cookie. A capacidade de personalizar os nomes de esquema de cookie de identidade é removida.

Por exemplo, os projetos 1. x usam [injeção de Construtor](xref:mvc/controllers/dependency-injection#constructor-injection) para passar um parâmetro `IdentityCookieOptions` para *AccountController.cs* e *ManageController.cs*. O esquema de autenticação de cookie externo é acessado na instância fornecida:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

A injeção de Construtor mencionada torna-se desnecessária em projetos 2,0 e o campo `_externalCookieScheme` pode ser excluído:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

os projetos 1. x usaram o campo `_externalCookieScheme` da seguinte maneira:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Em 2,0 projetos, substitua o código anterior pelo seguinte. A constante de `IdentityConstants.ExternalScheme` pode ser usada diretamente.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Resolva a chamada de `SignOutAsync` recém adicionada importando o namespace a seguir:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Adicionar propriedades de navegação do IdentityUser POCO

As propriedades de navegação principal do Entity Framework (EF) da base `IdentityUser` POCO (objeto comum CLR) foram removidas. Se o projeto 1. x usou essas propriedades, adicione-as manualmente de volta ao projeto 2,0:

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

Para evitar chaves estrangeiras duplicadas ao executar EF Core migrações, adicione o seguinte ao método da classe `IdentityDbContext` ' `OnModelCreating` (após a chamada `base.OnModelCreating();`):

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>Substituir GetExternalAuthenticationSchemes

O método síncrono `GetExternalAuthenticationSchemes` foi removido em favor de uma versão assíncrona. os projetos 1. x têm o seguinte código em *Controllers/ManageController. cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Esse método aparece em *views/Account/Login. cshtml* também:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

Em 2,0 projetos, use o método <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*>. A alteração em *ManageController.cs* é semelhante ao seguinte código:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

Em *login. cshtml*, a propriedade `AuthenticationScheme` acessada no loop de `foreach` muda para `Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Alteração da propriedade ManageLoginsViewModel

Um objeto `ManageLoginsViewModel` é usado na ação `ManageLogins` de *ManageController.cs*. Em projetos 1. x, o tipo de retorno da propriedade `OtherLogins` do objeto é `IList<AuthenticationDescription>`. Esse tipo de retorno requer uma importação de `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

Em 2,0 projetos, o tipo de retorno muda para `IList<AuthenticationScheme>`. Esse novo tipo de retorno requer a substituição do `Microsoft.AspNetCore.Http.Authentication` importação por uma importação de `Microsoft.AspNetCore.Authentication`.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, consulte o artigo [discussão do problema de autenticação 2,0](https://github.com/aspnet/Security/issues/1338) no github.
