---
title: Migrar autenticação e identidade para o ASP.NET Core 2.0
author: scottaddie
description: Este artigo descreve as etapas mais comuns para Migrando do ASP.NET Core 1.x autenticação e identidade para o ASP.NET Core 2.0.
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: d11d41c82236436096660a24df81a3df4da0fb8e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892203"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Migrar autenticação e identidade para o ASP.NET Core 2.0

Por [Scott Addie](https://github.com/scottaddie) e [Kung Hao](https://github.com/HaoK)

ASP.NET Core 2.0 tem um novo modelo para autenticação e [identidade](xref:security/authentication/identity) que simplifica a configuração por meio de serviços. Aplicativos ASP.NET Core 1.x que usam autenticação ou a identidade podem ser atualizados para usar o novo modelo, conforme descrito abaixo.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Middleware de autenticação e serviços

Em projetos 1.x, a autenticação é configurada por meio do middleware. Um método de middleware é invocado para cada esquema de autenticação que você deseja dar suporte.

O exemplo a seguir 1.x configura a autenticação do Facebook com identidade no *Startup.cs*:

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

Em projetos do 2.0, a autenticação é configurada por meio de serviços. Cada esquema de autenticação é registrada na `ConfigureServices` método de *Startup.cs*. O `UseIdentity` o método é substituído por `UseAuthentication`.

O exemplo a seguir 2.0 configura a autenticação do Facebook com identidade no *Startup.cs*:

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

O `UseAuthentication` método adiciona um componente de middleware de autenticação único que é responsável pela autenticação automática e o tratamento de solicitações de autenticação remota. Ele substitui todos os componentes de middleware individuais com um componente de middleware simples e comum.

Veja abaixo as instruções de migração de 2.0 para cada esquema de autenticação principal.

### <a name="cookie-based-authentication"></a>Autenticação baseada em cookie

Selecione uma das duas opções abaixo e faça as alterações necessárias na *Startup.cs*:

1. Usar cookies com identidade
    - Substitua `UseIdentity` com `UseAuthentication` no `Configure` método:

        ```csharp
        app.UseAuthentication();
        ```

    - Invocar o `AddIdentity` método no `ConfigureServices` método para adicionar os serviços de autenticação de cookie.
    - Opcionalmente, invocar o `ConfigureApplicationCookie` ou `ConfigureExternalCookie` método no `ConfigureServices` método ajustar as configurações de cookie de identidade.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Usar cookies sem o Identity
    - Substitua os `UseCookieAuthentication` chamada de método na `Configure` método com `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - Invocar o `AddAuthentication` e `AddCookie` métodos no `ConfigureServices` método:

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

### <a name="jwt-bearer-authentication"></a>JWT Bearer Authentication

Faça as seguintes alterações na *Startup.cs*:
- Substitua os `UseJwtBearerAuthentication` chamada de método na `Configure` método com `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invocar o `AddJwtBearer` método no `ConfigureServices` método:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Este trecho de código não usa a identidade, portanto, o esquema padrão deve ser definido, passando `JwtBearerDefaults.AuthenticationScheme` para o `AddAuthentication` método.

### <a name="openid-connect-oidc-authentication"></a>Autenticação OIDC (OpenID Connect)

Faça as seguintes alterações na *Startup.cs*:

- Substitua os `UseOpenIdConnectAuthentication` chamada de método na `Configure` método com `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invocar o `AddOpenIdConnect` método no `ConfigureServices` método:

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

### <a name="facebook-authentication"></a>Autenticação do Facebook

Faça as seguintes alterações na *Startup.cs*:
- Substitua os `UseFacebookAuthentication` chamada de método na `Configure` método com `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invocar o `AddFacebook` método no `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Autenticação do Google

Faça as seguintes alterações na *Startup.cs*:
- Substitua os `UseGoogleAuthentication` chamada de método na `Configure` método com `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invocar o `AddGoogle` método no `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Autenticação da Microsoft Account

Faça as seguintes alterações na *Startup.cs*:
- Substitua os `UseMicrosoftAccountAuthentication` chamada de método na `Configure` método com `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invocar o `AddMicrosoftAccount` método no `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Autenticação do Twitter

Faça as seguintes alterações na *Startup.cs*:
- Substitua os `UseTwitterAuthentication` chamada de método na `Configure` método com `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Invocar o `AddTwitter` método no `ConfigureServices` método:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Esquemas de autenticação padrão de configuração

No 1.x, o `AutomaticAuthenticate` e `AutomaticChallenge` propriedades da [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) classe base foram deve ser definida em um esquema de autenticação. Não havia nenhuma boa maneira para impor isso.

No 2.0, essas duas propriedades foram removidas como propriedades individual `AuthenticationOptions` instância. Eles podem ser configurados na `AddAuthentication` chamada de método dentro a `ConfigureServices` método de *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

No trecho de código anterior, o esquema padrão é definido como `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").

Como alternativa, use uma versão sobrecarregada do `AddAuthentication` método para definir mais de uma propriedade. No exemplo a seguir o método sobrecarregado, o esquema padrão é definido como `CookieAuthenticationDefaults.AuthenticationScheme`. O esquema de autenticação como alternativa, pode ser especificado dentro de seu indivíduo `[Authorize]` atributos ou as políticas de autorização.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Defina um esquema padrão do 2.0, se uma das seguintes condições for verdadeira:
- Você deseja que o usuário a ser conectado automaticamente
- Você usa o `[Authorize]` políticas de autorização ou atributo sem especificar esquemas

Uma exceção a essa regra é o `AddIdentity` método. Esse método adiciona cookies para você e define o padrão autenticar e esquemas de desafio para o cookie do aplicativo `IdentityConstants.ApplicationScheme`. Além disso, ele define o esquema de entrada padrão para o cookie externo `IdentityConstants.ExternalScheme`.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Usar extensões de autenticação HttpContext

O `IAuthenticationManager` interface é o ponto de entrada principal para o sistema de autenticação de 1. x. Ele foi substituído por um novo conjunto de `HttpContext` métodos de extensão no `Microsoft.AspNetCore.Authentication` namespace.

Por exemplo, projetos do 1.x referência um `Authentication` propriedade:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Em 2.0 projetos, importe as `Microsoft.AspNetCore.Authentication` namespace e exclua o `Authentication` referências de propriedade:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Autenticação do Windows (http. sys / IISIntegration)

Há duas variações de autenticação do Windows:
1. O host permite que somente usuários autenticados
2. O host permite que ambos anônimos e usuários autenticados

A primeira descrita acima não é afetada pelas alterações de 2.0.

A segunda descrita acima é afetada pelas alterações de 2.0. Por exemplo, você pode ser permitir que usuários anônimos em seu aplicativo no IIS ou [HTTP. sys](xref:fundamentals/servers/httpsys) mas autorizar usuários no nível do controlador de camada. Nesse cenário, defina o esquema padrão `IISDefaults.AuthenticationScheme` no `Startup.ConfigureServices` método:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

Falha ao definir o esquema padrão adequadamente impede que a solicitação de autorização de desafio do trabalho.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Instâncias de IdentityCookieOptions

Um efeito colateral das 2.0 alterações é o comutador que usa o chamado opções em vez de instâncias de opções de cookie. A capacidade de personalizar os nomes de esquema do cookie de identidade é removida.

Por exemplo, 1. x projetos usam [injeção de construtor](xref:mvc/controllers/dependency-injection#constructor-injection) para passar um `IdentityCookieOptions` parâmetro em *AccountController.cs*. O esquema de autenticação de cookie externa é acessado da instância fornecida:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

A injeção de construtor mencionados anteriormente se torna desnecessária em 2.0 projetos e o `_externalCookieScheme` campo pode ser excluído:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

O `IdentityConstants.ExternalScheme` constante pode ser usada diretamente:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Adicionar propriedades de navegação IdentityUser POCO

As propriedades de navegação do Entity Framework (EF) Core da base de `IdentityUser` POCO (objeto Plain Old CLR) foram removidos. Se seu projeto 1.x usou essas propriedades, manualmente adicione ao projeto 2.0:

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

Para impedir que as chaves estrangeiras duplicadas ao executar migrações do EF Core, adicione o seguinte ao seu `IdentityDbContext` classe `OnModelCreating` método (depois que o `base.OnModelCreating();` chamar):

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

O método síncrono `GetExternalAuthenticationSchemes` foi removido para uma versão assíncrona. projetos do 1.x tem o seguinte código *ManageController.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Este método é exibido no *cshtml* muito:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

Em projetos do 2.0, use o `GetExternalAuthenticationSchemesAsync` método:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

Na *cshtml*, o `AuthenticationScheme` propriedade acessada no `foreach` loop é alterado para `Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Alteração da propriedade ManageLoginsViewModel

Um `ManageLoginsViewModel` objeto é usado em de `ManageLogins` ação de *ManageController.cs*. Em projetos 1.x, o objeto `OtherLogins` propriedade de tipo de retorno é `IList<AuthenticationDescription>`. Esse tipo de retorno requer uma importação de `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

Em projetos do 2.0, o tipo de retorno é alterado para `IList<AuthenticationScheme>`. Esse novo tipo de retorno requer substituindo o `Microsoft.AspNetCore.Http.Authentication` importação de um `Microsoft.AspNetCore.Authentication` importar.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais detalhes e discussões, consulte o [discussão do Auth 2.0](https://github.com/aspnet/Security/issues/1338) problema no GitHub.
