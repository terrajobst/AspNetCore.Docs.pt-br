---
title: Compartilhar cookies de autenticação entre aplicativos do ASP.NET
author: rick-anderson
description: Aprenda a compartilhar cookies de autenticação entre o ASP.NET 4. x e aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2019
uid: security/cookie-sharing
ms.openlocfilehash: b2f906ac97fe79b2a66a5ab709bcbcb03ab8cc39
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223918"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Compartilhar cookies de autenticação entre aplicativos do ASP.NET

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)

Sites geralmente consistem em aplicativos web individuais trabalhando juntos. Para fornecer uma experiência de logon único (SSO), aplicativos web dentro de um site devem compartilhar cookies de autenticação. Para dar suporte a esse cenário, a pilha de proteção de dados permite o compartilhamento de autenticação de cookie do Katana e tíquetes de autenticação de cookie do ASP.NET Core.

Nos exemplos a seguir:

* O nome do cookie de autenticação é definido como um valor comum de `.AspNet.SharedCookie`.
* O `AuthenticationType` é definido como `Identity.Application` explicitamente ou por padrão.
* Um nome de aplicativo comum é usado para permitir que o sistema de proteção de dados compartilhar as chaves de proteção de dados (`SharedCookieApp`).
* `Identity.Application` é usado como o esquema de autenticação. Qualquer esquema é usada, ele deve ser usado de forma consistente *dentro e entre* os aplicativos de cookie compartilhado como o esquema padrão ou ao defini-lo. O esquema é usado ao criptografar e descriptografar cookies, portanto, um esquema consistente deve ser usado entre aplicativos.
* Um comum [chave da proteção de dados](xref:security/data-protection/implementation/key-management) armazenamento local é usado.
  * Em aplicativos ASP.NET Core, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> é usado para definir o local de armazenamento de chaves.
  * Em aplicativos do .NET Framework, o Middleware de autenticação de Cookie usa uma implementação de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>. `DataProtectionProvider` fornece serviços de proteção de dados para a criptografia e descriptografia de dados de conteúdo do cookie de autenticação. O `DataProtectionProvider` instância é isolada do sistema de proteção de dados usado por outras partes do aplicativo. [DataProtectionProvider.Create (DirectoryInfo, uma ação\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) aceita um <xref:System.IO.DirectoryInfo> para especificar o local para armazenamento de chaves de proteção de dados.
* `DataProtectionProvider` requer o [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) pacote do NuGet:
  * Em aplicativos do ASP.NET Core 2.x, fazer referência a [metapacote Microsoft](xref:fundamentals/metapackage-app).
  * Em aplicativos do .NET Framework, adicione uma referência de pacote ao [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Define o nome comum do aplicativo.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Compartilhar cookies de autenticação entre aplicativos do ASP.NET Core

Ao usar a identidade do ASP.NET Core:

* Chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos. Um local comum do armazenamento de chaves é fornecido para o <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> método nos exemplos a seguir. Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar um nome de aplicativo compartilhado comum (`SharedCookieApp` nos exemplos a seguir). Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.
* Use o <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensão para configurar o serviço de proteção de dados para cookies.
* No exemplo a seguir, o tipo de autenticação está definido como `Identity.Application` por padrão.

No `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

Ao usar cookies diretamente sem a identidade do ASP.NET Core, configurar a proteção de dados e autenticação no `Startup.ConfigureServices`. No exemplo a seguir, o tipo de autenticação está definido como `Identity.Application`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

Ao hospedar aplicativos que compartilham cookies entre subdomínios, especifique um domínio comum na [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) propriedade. Compartilhar cookies entre aplicativos no `contoso.com`, como `first_subdomain.contoso.com` e `second_subdomain.contoso.com`, especifique o `Cookie.Domain` como `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Criptografar as chaves de proteção de dados em repouso

Para implantações de produção, configure o `DataProtectionProvider` para criptografar as chaves em repouso com DPAPI ou um X509Certificate. Para obter mais informações, consulte <xref:security/data-protection/implementation/key-encryption-at-rest>. No exemplo a seguir, uma impressão digital do certificado é fornecida para <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Compartilhar cookies de autenticação entre o ASP.NET 4. x e aplicativos ASP.NET Core

Aplicativos ASP.NET 4.x que usam o Middleware de autenticação de Cookie do Katana podem ser configurados para gerar os cookies de autenticação que são compatíveis com o Middleware de autenticação de Cookie do ASP.NET Core. Isso permite que a atualização de aplicativos individuais de um grande site em várias etapas, fornecendo uma experiência de SSO suave em todo o site.

Quando um aplicativo usa o Middleware de autenticação de Cookie do Katana, ele chama `UseCookieAuthentication` do projeto *Startup.Auth.cs* arquivo. Projetos de aplicativo web do ASP.NET 4.x criado com o Visual Studio 2013 e posterior, use o Middleware de autenticação de Cookie do Katana por padrão. Embora `UseCookieAuthentication` está obsoleto e sem suporte para aplicativos ASP.NET Core, chamando `UseCookieAuthentication` em um ASP.NET 4.x aplicativo que usa o Middleware de autenticação de Cookie do Katana é válido.

Um aplicativo do ASP.NET 4. x deve ter como destino do .NET Framework 4.5.1 ou posterior. Caso contrário, não conseguir instalar os pacotes NuGet necessários.

Para compartilhar cookies de autenticação entre um aplicativo do ASP.NET 4.x e um aplicativo ASP.NET Core, configure o aplicativo ASP.NET Core, conforme indicado na [compartilhar cookies de autenticação entre aplicativos do ASP.NET Core](#share-authentication-cookies-among-aspnet-core-apps) seção e, em seguida, configurar o aplicativo ASP.NET 4. x como segue.

Confirme que os pacotes do aplicativo são atualizados para as versões mais recentes. Instalar o [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) pacote em cada aplicativo do ASP.NET 4. x.

Localize e modifique a chamada para `UseCookieAuthentication`:

* Alterar o nome do cookie para corresponder ao nome usado pelo Middleware de autenticação de Cookie do ASP.NET Core (`.AspNet.SharedCookie` no exemplo).
* No exemplo a seguir, o tipo de autenticação está definido como `Identity.Application`.
* Fornecer uma instância de um `DataProtectionProvider` inicializado para o local de armazenamento de chaves de proteção de dados comuns.
* Confirme se o nome do aplicativo é definido como o nome de aplicativo comum usado por todos os aplicativos que compartilham os cookies de autenticação (`SharedCookieApp` no exemplo).

Se não configurar `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, defina <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> para uma declaração que distingue de usuários exclusivos.

*App_Start/Startup.Auth.CS*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create({PATH TO COMMON KEY RING FOLDER},
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Ao gerar uma identidade de usuário, o tipo de autenticação (`Identity.Application`) deve corresponder ao tipo definido no `AuthenticationType` definidas com `UseCookieAuthentication` na *App_Start/Startup.Auth.CS*.

*Models/IdentityModels.cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Usar um banco de dados de usuário comum

Quando os aplicativos usam a mesma identidade de esquema (mesma versão da identidade), confirme que o sistema de identidade para cada aplicativo está apontado para o mesmo banco de dados do usuário. Caso contrário, o sistema de identidade gera falhas em tempo de execução quando ele tenta corresponder as informações no cookie de autenticação em relação as informações em seu banco de dados.

Quando o esquema de identidade é diferente entre aplicativos, geralmente porque os aplicativos estão usando versões diferentes de identidade, compartilhamento de um banco de dados comuns, com base na versão mais recente de identidade não é possível sem remapeamento e adição de colunas em esquemas de identidade do outro aplicativo. Geralmente é mais eficiente para atualizar os outros aplicativos para usar a versão mais recente de identidade para que um banco de dados comum pode ser compartilhado por aplicativos.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/web-farm>
