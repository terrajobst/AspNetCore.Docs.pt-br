---
title: Compartilhar cookies entre aplicativos com o ASP.NET e ASP.NET Core
author: rick-anderson
description: Aprenda a compartilhar cookies de autenticação entre o ASP.NET 4. x e aplicativos ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7a049ed8787808e228859afc051b8697a6261c21
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068304"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a>Compartilhar cookies entre aplicativos com o ASP.NET e ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)

Sites geralmente consistem em aplicativos web individuais trabalhando juntos. Para fornecer uma experiência de logon único (SSO), aplicativos web dentro de um site devem compartilhar cookies de autenticação. Para dar suporte a esse cenário, a pilha de proteção de dados permite o compartilhamento de autenticação de cookie do Katana e tíquetes de autenticação de cookie do ASP.NET Core.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([como baixar](xref:index#how-to-download-a-sample))

O exemplo ilustra o cookie compartilhando entre três aplicativos que usam a autenticação de cookie:

* Aplicativo do ASP.NET Core 2.0 as páginas do Razor sem usar [ASP.NET Core Identity](xref:security/authentication/identity)
* Aplicativo MVC do ASP.NET Core 2.0 com o ASP.NET Core Identity
* Aplicativo MVC do ASP.NET Framework 4.6.1 com o ASP.NET Identity

Nos exemplos a seguir:

* O nome do cookie de autenticação é definido como um valor comum de `.AspNet.SharedCookie`.
* O `AuthenticationType` é definido como `Identity.Application` explicitamente ou por padrão.
* Um nome de aplicativo comum é usado para permitir que o sistema de proteção de dados compartilhar as chaves de proteção de dados (`SharedCookieApp`).
* `Identity.Application` é usado como o esquema de autenticação. Qualquer esquema é usada, ele deve ser usado de forma consistente *dentro e entre* os aplicativos de cookie compartilhado como o esquema padrão ou ao defini-lo. O esquema é usado ao criptografar e descriptografar cookies, portanto, um esquema consistente deve ser usado entre aplicativos.
* Um comum [chave da proteção de dados](xref:security/data-protection/implementation/key-management) armazenamento local é usado. O aplicativo de exemplo usa uma pasta chamada *token de autenticação* na raiz da solução para manter as chaves de proteção de dados.
* Em aplicativos ASP.NET Core, [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) é usado para definir o local de armazenamento de chaves. [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) é usado para configurar um nome de aplicativo compartilhado comum.
* No aplicativo do .NET Framework, o middleware de autenticação de cookie usa uma implementação [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider). `DataProtectionProvider` fornece serviços de proteção de dados para a criptografia e descriptografia de dados de conteúdo do cookie de autenticação. O `DataProtectionProvider` instância é isolada do sistema de proteção de dados usado por outras partes do aplicativo.
  * [DataProtectionProvider.Create (DirectoryInfo, uma ação\<IDataProtectionBuilder >)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__) aceita uma [DirectoryInfo](/dotnet/api/system.io.directoryinfo) para especificar o local para armazenamento de chaves de proteção de dados. O aplicativo de exemplo fornece o caminho da *token de autenticação* pasta a ser `DirectoryInfo`. [DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) define o nome comum do aplicativo.
  * [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) exige as [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) pacote do NuGet. Para obter esse pacote para o ASP.NET Core 2.1 e posteriores aplicativos, fazer referência a [metapacote Microsoft](xref:fundamentals/metapackage-app). Ao direcionar o .NET Framework, adicione uma referência de pacote ao `Microsoft.AspNetCore.DataProtection.Extensions`.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Compartilhar cookies de autenticação entre aplicativos do ASP.NET Core

Ao usar a identidade do ASP.NET Core:

::: moniker range=">= aspnetcore-2.0"

No `ConfigureServices` método, use o [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) método de extensão para configurar o serviço de proteção de dados para cookies.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

Chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos. Em aplicativos de exemplo, `GetKeyRingDirInfo` retorna o local de armazenamento de chaves comuns para o [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) método. Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) para configurar um nome de aplicativo compartilhado comum (`SharedCookieApp` no exemplo). Para obter mais informações, consulte [Configurando a proteção de dados](xref:security/data-protection/configuration/overview).

Ao hospedar aplicativos que compartilham cookies entre subdomínios, especifique um domínio comum na [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) propriedade. Compartilhar cookies entre aplicativos no `contoso.com`, como `first_subdomain.contoso.com` e `second_subdomain.contoso.com`, especifique o `Cookie.Domain` como `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

Consulte a *CookieAuthWithIdentity.Core* do projeto na [código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([como baixar](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

No `Configure` método, use o [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) configurar:

* O serviço de proteção de dados para cookies.
* O `AuthenticationScheme` para coincidir com o ASP.NET 4. x.

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
{
    options.Cookies.ApplicationCookie.AuthenticationScheme = 
        "ApplicationCookie";

    var protectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"));

    options.Cookies.ApplicationCookie.DataProtectionProvider = 
        protectionProvider;

    options.Cookies.ApplicationCookie.TicketDataFormat = 
        new TicketDataFormat(protectionProvider.CreateProtector(
            "Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", 
            "Cookies", 
            "v2"));
});
```

::: moniker-end

Ao usar cookies diretamente:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

Chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos. Em aplicativos de exemplo, `GetKeyRingDirInfo` retorna o local de armazenamento de chaves comuns para o [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) método. Use [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) para configurar um nome de aplicativo compartilhado comum (`SharedCookieApp` no exemplo). Para obter mais informações, consulte [Configurando a proteção de dados](xref:security/data-protection/configuration/overview).

Ao hospedar aplicativos que compartilham cookies entre subdomínios, especifique um domínio comum na [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) propriedade. Compartilhar cookies entre aplicativos no `contoso.com`, como `first_subdomain.contoso.com` e `second_subdomain.contoso.com`, especifique o `Cookie.Domain` como `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

Consulte a *CookieAuth.Core* do projeto na [código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([como baixar](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
});
```

::: moniker-end

## <a name="encrypting-data-protection-keys-at-rest"></a>Criptografar as chaves de proteção de dados em repouso

Para implantações de produção, configure o `DataProtectionProvider` para criptografar as chaves em repouso com DPAPI ou um X509Certificate. Ver [chave de criptografia em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais informações.

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("thumbprint");
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = DataProtectionProvider.Create(
        new DirectoryInfo(@"PATH_TO_KEY_RING"),
        configure =>
        {
            configure.ProtectKeysWithCertificate("thumbprint");
        })
});
```

::: moniker-end

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Compartilhamento de cookies de autenticação entre o ASP.NET 4. x e aplicativos ASP.NET Core

Os aplicativos do ASP.NET 4.x que usam o middleware de autenticação de cookie do Katana podem ser configurados para gerar os cookies de autenticação que são compatíveis com o middleware de autenticação de cookie do ASP.NET Core. Isso permite atualizar aplicativos individuais de um grande site gradativamente, fornecendo uma experiência de SSO suave em todo o site.

Quando um aplicativo usa o middleware de autenticação de cookie do Katana, ele chama `UseCookieAuthentication` do projeto *Startup.Auth.cs* arquivo. Projetos de aplicativo web do ASP.NET 4.x criado com o Visual Studio 2013 e posterior, use o middleware de autenticação de cookie do Katana por padrão. Embora `UseCookieAuthentication` está obsoleto e sem suporte para aplicativos ASP.NET Core, chamando `UseCookieAuthentication` em um aplicativo do ASP.NET 4.x que usa o Katana middleware de autenticação de cookie é válido.

Um aplicativo do ASP.NET 4. x deve ter como destino do .NET Framework 4.5.1 ou posterior. Caso contrário, não conseguir instalar os pacotes NuGet necessários.

Para compartilhar cookies de autenticação entre um aplicativo do ASP.NET 4.x e um aplicativo ASP.NET Core, configurar o aplicativo ASP.NET Core, conforme mencionado acima e, em seguida, configurar o aplicativo do ASP.NET 4.x, seguindo estas etapas:

1. Instale o pacote [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) em cada aplicativo do ASP.NET 4. x.

2. Na *Startup.Auth.cs*, localize a chamada para `UseCookieAuthentication` e modificá-lo da seguinte maneira. Altere o nome do cookie para corresponder ao nome usado pelo middleware de autenticação de cookie do ASP.NET Core. Fornecer uma instância de um `DataProtectionProvider` inicializado para o local de armazenamento de chaves de proteção de dados comuns. Certifique-se de que o nome do aplicativo é definido como o nome de aplicativo comum usado por todos os aplicativos que compartilham os cookies, `SharedCookieApp` no aplicativo de exemplo.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

Consulte a *CookieAuthWithIdentity.NETFramework* do projeto na [código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([como baixar](xref:index#how-to-download-a-sample)).

Ao gerar uma identidade de usuário, o tipo de autenticação deve corresponder ao tipo definido em `AuthenticationType` definido com `UseCookieAuthentication`.

*Models/IdentityModels.cs*:

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a>Usar um banco de dados de usuário comum

Confirme que o sistema de identidade para cada aplicativo está apontado para o mesmo banco de dados do usuário. Caso contrário, o sistema de identidade gera falhas em tempo de execução quando ele tenta corresponder as informações no cookie de autenticação em relação as informações em seu banco de dados.

## <a name="additional-resources"></a>Recursos adicionais

<xref:host-and-deploy/web-farm>
