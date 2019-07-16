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
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="c285c-103">Compartilhar cookies de autenticação entre aplicativos do ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c285c-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="c285c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c285c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c285c-105">Sites geralmente consistem em aplicativos web individuais trabalhando juntos.</span><span class="sxs-lookup"><span data-stu-id="c285c-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="c285c-106">Para fornecer uma experiência de logon único (SSO), aplicativos web dentro de um site devem compartilhar cookies de autenticação.</span><span class="sxs-lookup"><span data-stu-id="c285c-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="c285c-107">Para dar suporte a esse cenário, a pilha de proteção de dados permite o compartilhamento de autenticação de cookie do Katana e tíquetes de autenticação de cookie do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c285c-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="c285c-108">Nos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="c285c-108">In the examples that follow:</span></span>

* <span data-ttu-id="c285c-109">O nome do cookie de autenticação é definido como um valor comum de `.AspNet.SharedCookie`.</span><span class="sxs-lookup"><span data-stu-id="c285c-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="c285c-110">O `AuthenticationType` é definido como `Identity.Application` explicitamente ou por padrão.</span><span class="sxs-lookup"><span data-stu-id="c285c-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="c285c-111">Um nome de aplicativo comum é usado para permitir que o sistema de proteção de dados compartilhar as chaves de proteção de dados (`SharedCookieApp`).</span><span class="sxs-lookup"><span data-stu-id="c285c-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="c285c-112">`Identity.Application` é usado como o esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="c285c-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="c285c-113">Qualquer esquema é usada, ele deve ser usado de forma consistente *dentro e entre* os aplicativos de cookie compartilhado como o esquema padrão ou ao defini-lo.</span><span class="sxs-lookup"><span data-stu-id="c285c-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="c285c-114">O esquema é usado ao criptografar e descriptografar cookies, portanto, um esquema consistente deve ser usado entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c285c-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="c285c-115">Um comum [chave da proteção de dados](xref:security/data-protection/implementation/key-management) armazenamento local é usado.</span><span class="sxs-lookup"><span data-stu-id="c285c-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="c285c-116">Em aplicativos ASP.NET Core, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> é usado para definir o local de armazenamento de chaves.</span><span class="sxs-lookup"><span data-stu-id="c285c-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="c285c-117">Em aplicativos do .NET Framework, o Middleware de autenticação de Cookie usa uma implementação de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span><span class="sxs-lookup"><span data-stu-id="c285c-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="c285c-118">`DataProtectionProvider` fornece serviços de proteção de dados para a criptografia e descriptografia de dados de conteúdo do cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="c285c-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="c285c-119">O `DataProtectionProvider` instância é isolada do sistema de proteção de dados usado por outras partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c285c-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="c285c-120">[DataProtectionProvider.Create (DirectoryInfo, uma ação\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) aceita um <xref:System.IO.DirectoryInfo> para especificar o local para armazenamento de chaves de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="c285c-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="c285c-121">`DataProtectionProvider` requer o [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) pacote do NuGet:</span><span class="sxs-lookup"><span data-stu-id="c285c-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="c285c-122">Em aplicativos do ASP.NET Core 2.x, fazer referência a [metapacote Microsoft](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="c285c-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="c285c-123">Em aplicativos do .NET Framework, adicione uma referência de pacote ao [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="c285c-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="c285c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Define o nome comum do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c285c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="c285c-125">Compartilhar cookies de autenticação entre aplicativos do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c285c-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="c285c-126">Ao usar a identidade do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c285c-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="c285c-127">Chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c285c-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="c285c-128">Um local comum do armazenamento de chaves é fornecido para o <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> método nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="c285c-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="c285c-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar um nome de aplicativo compartilhado comum (`SharedCookieApp` nos exemplos a seguir).</span><span class="sxs-lookup"><span data-stu-id="c285c-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="c285c-130">Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="c285c-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="c285c-131">Use o <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensão para configurar o serviço de proteção de dados para cookies.</span><span class="sxs-lookup"><span data-stu-id="c285c-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="c285c-132">No exemplo a seguir, o tipo de autenticação está definido como `Identity.Application` por padrão.</span><span class="sxs-lookup"><span data-stu-id="c285c-132">In the following example, the authentication type is set to `Identity.Application` by default.</span></span>

<span data-ttu-id="c285c-133">No `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c285c-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="c285c-134">Ao usar cookies diretamente sem a identidade do ASP.NET Core, configurar a proteção de dados e autenticação no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c285c-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c285c-135">No exemplo a seguir, o tipo de autenticação está definido como `Identity.Application`:</span><span class="sxs-lookup"><span data-stu-id="c285c-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="c285c-136">Ao hospedar aplicativos que compartilham cookies entre subdomínios, especifique um domínio comum na [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) propriedade.</span><span class="sxs-lookup"><span data-stu-id="c285c-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="c285c-137">Compartilhar cookies entre aplicativos no `contoso.com`, como `first_subdomain.contoso.com` e `second_subdomain.contoso.com`, especifique o `Cookie.Domain` como `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="c285c-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="c285c-138">Criptografar as chaves de proteção de dados em repouso</span><span class="sxs-lookup"><span data-stu-id="c285c-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="c285c-139">Para implantações de produção, configure o `DataProtectionProvider` para criptografar as chaves em repouso com DPAPI ou um X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="c285c-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="c285c-140">Para obter mais informações, consulte <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="c285c-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="c285c-141">No exemplo a seguir, uma impressão digital do certificado é fornecida para <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span><span class="sxs-lookup"><span data-stu-id="c285c-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="c285c-142">Compartilhar cookies de autenticação entre o ASP.NET 4. x e aplicativos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c285c-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="c285c-143">Aplicativos ASP.NET 4.x que usam o Middleware de autenticação de Cookie do Katana podem ser configurados para gerar os cookies de autenticação que são compatíveis com o Middleware de autenticação de Cookie do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c285c-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="c285c-144">Isso permite que a atualização de aplicativos individuais de um grande site em várias etapas, fornecendo uma experiência de SSO suave em todo o site.</span><span class="sxs-lookup"><span data-stu-id="c285c-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="c285c-145">Quando um aplicativo usa o Middleware de autenticação de Cookie do Katana, ele chama `UseCookieAuthentication` do projeto *Startup.Auth.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="c285c-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="c285c-146">Projetos de aplicativo web do ASP.NET 4.x criado com o Visual Studio 2013 e posterior, use o Middleware de autenticação de Cookie do Katana por padrão.</span><span class="sxs-lookup"><span data-stu-id="c285c-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="c285c-147">Embora `UseCookieAuthentication` está obsoleto e sem suporte para aplicativos ASP.NET Core, chamando `UseCookieAuthentication` em um ASP.NET 4.x aplicativo que usa o Middleware de autenticação de Cookie do Katana é válido.</span><span class="sxs-lookup"><span data-stu-id="c285c-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="c285c-148">Um aplicativo do ASP.NET 4. x deve ter como destino do .NET Framework 4.5.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="c285c-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="c285c-149">Caso contrário, não conseguir instalar os pacotes NuGet necessários.</span><span class="sxs-lookup"><span data-stu-id="c285c-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="c285c-150">Para compartilhar cookies de autenticação entre um aplicativo do ASP.NET 4.x e um aplicativo ASP.NET Core, configure o aplicativo ASP.NET Core, conforme indicado na [compartilhar cookies de autenticação entre aplicativos do ASP.NET Core](#share-authentication-cookies-among-aspnet-core-apps) seção e, em seguida, configurar o aplicativo ASP.NET 4. x como segue.</span><span class="sxs-lookup"><span data-stu-id="c285c-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="c285c-151">Confirme que os pacotes do aplicativo são atualizados para as versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="c285c-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="c285c-152">Instalar o [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) pacote em cada aplicativo do ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="c285c-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="c285c-153">Localize e modifique a chamada para `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="c285c-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="c285c-154">Alterar o nome do cookie para corresponder ao nome usado pelo Middleware de autenticação de Cookie do ASP.NET Core (`.AspNet.SharedCookie` no exemplo).</span><span class="sxs-lookup"><span data-stu-id="c285c-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="c285c-155">No exemplo a seguir, o tipo de autenticação está definido como `Identity.Application`.</span><span class="sxs-lookup"><span data-stu-id="c285c-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="c285c-156">Fornecer uma instância de um `DataProtectionProvider` inicializado para o local de armazenamento de chaves de proteção de dados comuns.</span><span class="sxs-lookup"><span data-stu-id="c285c-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="c285c-157">Confirme se o nome do aplicativo é definido como o nome de aplicativo comum usado por todos os aplicativos que compartilham os cookies de autenticação (`SharedCookieApp` no exemplo).</span><span class="sxs-lookup"><span data-stu-id="c285c-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="c285c-158">Se não configurar `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, defina <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> para uma declaração que distingue de usuários exclusivos.</span><span class="sxs-lookup"><span data-stu-id="c285c-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="c285c-159">*App_Start/Startup.Auth.CS*:</span><span class="sxs-lookup"><span data-stu-id="c285c-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="c285c-160">Ao gerar uma identidade de usuário, o tipo de autenticação (`Identity.Application`) deve corresponder ao tipo definido no `AuthenticationType` definidas com `UseCookieAuthentication` na *App_Start/Startup.Auth.CS*.</span><span class="sxs-lookup"><span data-stu-id="c285c-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="c285c-161">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="c285c-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="c285c-162">Usar um banco de dados de usuário comum</span><span class="sxs-lookup"><span data-stu-id="c285c-162">Use a common user database</span></span>

<span data-ttu-id="c285c-163">Quando os aplicativos usam a mesma identidade de esquema (mesma versão da identidade), confirme que o sistema de identidade para cada aplicativo está apontado para o mesmo banco de dados do usuário.</span><span class="sxs-lookup"><span data-stu-id="c285c-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="c285c-164">Caso contrário, o sistema de identidade gera falhas em tempo de execução quando ele tenta corresponder as informações no cookie de autenticação em relação as informações em seu banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c285c-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="c285c-165">Quando o esquema de identidade é diferente entre aplicativos, geralmente porque os aplicativos estão usando versões diferentes de identidade, compartilhamento de um banco de dados comuns, com base na versão mais recente de identidade não é possível sem remapeamento e adição de colunas em esquemas de identidade do outro aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c285c-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="c285c-166">Geralmente é mais eficiente para atualizar os outros aplicativos para usar a versão mais recente de identidade para que um banco de dados comum pode ser compartilhado por aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c285c-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c285c-167">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c285c-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
