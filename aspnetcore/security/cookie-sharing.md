---
title: Compartilhar cookies de autenticação entre aplicativos ASP.NET
author: rick-anderson
description: Saiba como compartilhar cookies de autenticação entre os aplicativos ASP.NET 4. x e ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: security/cookie-sharing
ms.openlocfilehash: 1650afce5c371d0830bb207618b9c1495f0ce587
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022384"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="89ff3-103">Compartilhar cookies de autenticação entre aplicativos ASP.NET</span><span class="sxs-lookup"><span data-stu-id="89ff3-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="89ff3-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="89ff3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="89ff3-105">Os sites geralmente consistem em aplicativos Web individuais trabalhando juntos.</span><span class="sxs-lookup"><span data-stu-id="89ff3-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="89ff3-106">Para fornecer uma experiência de logon único (SSO), os aplicativos Web em um site devem compartilhar cookies de autenticação.</span><span class="sxs-lookup"><span data-stu-id="89ff3-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="89ff3-107">Para dar suporte a esse cenário, a pilha de proteção de dados permite compartilhar autenticação de cookie Katana e Tíquetes de autenticação de cookie de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89ff3-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="89ff3-108">Nos exemplos a seguir:</span><span class="sxs-lookup"><span data-stu-id="89ff3-108">In the examples that follow:</span></span>

* <span data-ttu-id="89ff3-109">O nome do cookie de autenticação é definido como um valor `.AspNet.SharedCookie`comum de.</span><span class="sxs-lookup"><span data-stu-id="89ff3-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="89ff3-110">O `AuthenticationType` é definido como `Identity.Application` explicitamente ou por padrão.</span><span class="sxs-lookup"><span data-stu-id="89ff3-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="89ff3-111">Um nome de aplicativo comum é usado para permitir que o sistema de proteção de dados Compartilhe chaves`SharedCookieApp`de proteção de dados ().</span><span class="sxs-lookup"><span data-stu-id="89ff3-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="89ff3-112">`Identity.Application`é usado como o esquema de autenticação.</span><span class="sxs-lookup"><span data-stu-id="89ff3-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="89ff3-113">Seja qual for o esquema usado, ele deve ser usado consistentemente *dentro e entre* os aplicativos de cookie compartilhado como o esquema padrão ou definindo-o explicitamente.</span><span class="sxs-lookup"><span data-stu-id="89ff3-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="89ff3-114">O esquema é usado ao criptografar e descriptografar cookies, portanto, um esquema consistente deve ser usado em aplicativos.</span><span class="sxs-lookup"><span data-stu-id="89ff3-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="89ff3-115">Um local de armazenamento de [chave de proteção de dados](xref:security/data-protection/implementation/key-management) comum é usado.</span><span class="sxs-lookup"><span data-stu-id="89ff3-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="89ff3-116">Em ASP.NET Core aplicativos, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> é usado para definir o local de armazenamento de chaves.</span><span class="sxs-lookup"><span data-stu-id="89ff3-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="89ff3-117">Em aplicativos .NET Framework, o middleware de autenticação de cookie usa uma <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>implementação de.</span><span class="sxs-lookup"><span data-stu-id="89ff3-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="89ff3-118">`DataProtectionProvider`fornece serviços de proteção de dados para criptografia e descriptografia de dados de carga do cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="89ff3-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="89ff3-119">A `DataProtectionProvider` instância é isolada do sistema de proteção de dados usado por outras partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89ff3-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="89ff3-120">[Dataprotectionprovider. criar (System. IO. DirectoryInfo, ação\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) aceita um <xref:System.IO.DirectoryInfo> para especificar o local para armazenamento de chave de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="89ff3-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="89ff3-121">`DataProtectionProvider`requer o pacote NuGet [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="89ff3-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="89ff3-122">Em aplicativos ASP.NET Core 2. x, referencie o [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="89ff3-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="89ff3-123">Em .NET Framework aplicativos, adicione uma referência de pacote a [Microsoft. AspNetCore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="89ff3-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="89ff3-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>define o nome comum do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="89ff3-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="89ff3-125">Compartilhar cookies de autenticação entre ASP.NET Core aplicativos</span><span class="sxs-lookup"><span data-stu-id="89ff3-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="89ff3-126">Ao usar a identidade de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="89ff3-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="89ff3-127">As chaves de proteção de dados e o nome do aplicativo devem ser compartilhados entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="89ff3-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="89ff3-128">Um local de armazenamento de chave comum é fornecido <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> para o método nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="89ff3-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="89ff3-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar um nome de aplicativo compartilhado comum`SharedCookieApp` (nos exemplos a seguir).</span><span class="sxs-lookup"><span data-stu-id="89ff3-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="89ff3-130">Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="89ff3-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="89ff3-131">Use o <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensão para configurar o serviço de proteção de dados para cookies.</span><span class="sxs-lookup"><span data-stu-id="89ff3-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="89ff3-132">O tipo de autenticação padrão `Identity.Application`é.</span><span class="sxs-lookup"><span data-stu-id="89ff3-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="89ff3-133">No `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="89ff3-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="89ff3-134">Ao usar cookies diretamente sem ASP.NET Core identidade, configure a proteção de dados e `Startup.ConfigureServices`a autenticação no.</span><span class="sxs-lookup"><span data-stu-id="89ff3-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="89ff3-135">No exemplo a seguir, o tipo de autenticação é definido `Identity.Application`como:</span><span class="sxs-lookup"><span data-stu-id="89ff3-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="89ff3-136">Ao hospedar aplicativos que compartilham cookies entre subdomínios, especifique um domínio comum na propriedade [Cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="89ff3-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="89ff3-137">Para compartilhar cookies entre aplicativos em `contoso.com`, `first_subdomain.contoso.com` como e `second_subdomain.contoso.com`, especifique o `Cookie.Domain` como `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="89ff3-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="89ff3-138">Criptografar chaves de proteção de dados em repouso</span><span class="sxs-lookup"><span data-stu-id="89ff3-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="89ff3-139">Para implantações de produção, `DataProtectionProvider` configure o para criptografar chaves em repouso com DPAPI ou um X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="89ff3-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="89ff3-140">Para obter mais informações, consulte <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="89ff3-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="89ff3-141">No exemplo a seguir, uma impressão digital do certificado é <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>fornecida para:</span><span class="sxs-lookup"><span data-stu-id="89ff3-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="89ff3-142">Compartilhar cookies de autenticação entre os aplicativos ASP.NET 4. x e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89ff3-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="89ff3-143">Os aplicativos ASP.NET 4. x que usam o middleware de autenticação de cookie Katana podem ser configurados para gerar cookies de autenticação que são compatíveis com o middleware de autenticação de cookie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="89ff3-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="89ff3-144">Isso permite atualizar aplicativos individuais de um site grande em várias etapas, ao mesmo tempo em que fornece uma experiência de SSO tranqüila em todo o site.</span><span class="sxs-lookup"><span data-stu-id="89ff3-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="89ff3-145">Quando um aplicativo usa o middleware de autenticação de cookie Katana, `UseCookieAuthentication` ele chama o arquivo *Startup.auth.cs* do projeto.</span><span class="sxs-lookup"><span data-stu-id="89ff3-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="89ff3-146">Os projetos de aplicativo Web ASP.NET 4. x criados com Visual Studio 2013 e posterior usam o middleware de autenticação de cookie Katana por padrão.</span><span class="sxs-lookup"><span data-stu-id="89ff3-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="89ff3-147">Embora `UseCookieAuthentication` o seja obsoleto e não tenha suporte para aplicativos ASP.NET Core, `UseCookieAuthentication` chamar um aplicativo ASP.NET 4. x que usa o middleware de autenticação de cookie Katana é válido.</span><span class="sxs-lookup"><span data-stu-id="89ff3-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="89ff3-148">Um aplicativo ASP.NET 4. x deve ter como destino .NET Framework 4.5.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="89ff3-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="89ff3-149">Caso contrário, os pacotes NuGet necessários falharão na instalação.</span><span class="sxs-lookup"><span data-stu-id="89ff3-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="89ff3-150">Para compartilhar cookies de autenticação entre um aplicativo ASP.NET 4. x e um aplicativo ASP.NET Core, configure o aplicativo ASP.NET Core conforme indicado na seção [compartilhar cookies de autenticação entre ASP.NET Core aplicativos](#share-authentication-cookies-among-aspnet-core-apps) e, em seguida, configure o aplicativo ASP.NET 4. x da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="89ff3-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="89ff3-151">Confirme se os pacotes do aplicativo foram atualizados para as versões mais recentes.</span><span class="sxs-lookup"><span data-stu-id="89ff3-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="89ff3-152">Instale o pacote [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) em cada aplicativo ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="89ff3-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="89ff3-153">Localize e modifique a chamada para `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="89ff3-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="89ff3-154">Altere o nome do cookie para corresponder ao nome usado pelo middleware de autenticação de cookie ASP.NET Core`.AspNet.SharedCookie` (no exemplo).</span><span class="sxs-lookup"><span data-stu-id="89ff3-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="89ff3-155">No exemplo a seguir, o tipo de autenticação é definido `Identity.Application`como.</span><span class="sxs-lookup"><span data-stu-id="89ff3-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="89ff3-156">Forneça uma instância de um `DataProtectionProvider` inicializado para o local de armazenamento de chave de proteção de dados comum.</span><span class="sxs-lookup"><span data-stu-id="89ff3-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="89ff3-157">Confirme se o nome do aplicativo está definido como o nome do aplicativo comum usado por todos os aplicativos que compartilham`SharedCookieApp` cookies de autenticação (no exemplo).</span><span class="sxs-lookup"><span data-stu-id="89ff3-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="89ff3-158">Se não estiver `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` Configurando <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> e `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, defina como uma declaração que diferencie usuários exclusivos.</span><span class="sxs-lookup"><span data-stu-id="89ff3-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="89ff3-159">*App_Start/Startup. auth. cs*:</span><span class="sxs-lookup"><span data-stu-id="89ff3-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="89ff3-160">Ao gerar uma identidade de usuário, o tipo de`Identity.Application`autenticação () deve corresponder ao tipo `AuthenticationType` definido em `UseCookieAuthentication` conjunto com em *App_Start/Startup. auth. cs*.</span><span class="sxs-lookup"><span data-stu-id="89ff3-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="89ff3-161">*Modelos/identitymodels. cs*:</span><span class="sxs-lookup"><span data-stu-id="89ff3-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="89ff3-162">Usar um banco de dados de usuário comum</span><span class="sxs-lookup"><span data-stu-id="89ff3-162">Use a common user database</span></span>

<span data-ttu-id="89ff3-163">Quando os aplicativos usam o mesmo esquema de identidade (mesma versão da identidade), confirme se o sistema de identidade de cada aplicativo é apontado para o mesmo banco de dados de usuário.</span><span class="sxs-lookup"><span data-stu-id="89ff3-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="89ff3-164">Caso contrário, o sistema de identidade produz falhas em tempo de execução quando tenta corresponder as informações no cookie de autenticação em relação às informações em seu banco de dados.</span><span class="sxs-lookup"><span data-stu-id="89ff3-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="89ff3-165">Quando o esquema de identidade é diferente entre aplicativos, geralmente porque os aplicativos estão usando versões de identidade diferentes, não é possível compartilhar um banco de dados comum com base na versão mais recente da identidade sem remapear e adicionar colunas nos esquemas de identidade de outros aplicativos.</span><span class="sxs-lookup"><span data-stu-id="89ff3-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="89ff3-166">Geralmente, é mais eficiente atualizar os outros aplicativos para usar a versão de identidade mais recente para que um banco de dados comum possa ser compartilhado pelos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="89ff3-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89ff3-167">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="89ff3-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
