---
title: Configurar a proteção de dados do ASP.NET Core
author: rick-anderson
description: Saiba como configurar a proteção de dados no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 380293f650c9548c286f98c0447c7ed08b918f2a
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007382"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="a8df5-103">Configurar a proteção de dados do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8df5-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="a8df5-104">Quando o sistema de proteção de dados é inicializado, ele aplica [as configurações padrão](xref:security/data-protection/configuration/default-settings) com base no ambiente operacional.</span><span class="sxs-lookup"><span data-stu-id="a8df5-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="a8df5-105">Essas configurações geralmente são apropriadas para aplicativos em execução em um único computador.</span><span class="sxs-lookup"><span data-stu-id="a8df5-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="a8df5-106">Há casos em que um desenvolvedor pode querer alterar as configurações padrão:</span><span class="sxs-lookup"><span data-stu-id="a8df5-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="a8df5-107">O aplicativo é distribuído entre vários computadores.</span><span class="sxs-lookup"><span data-stu-id="a8df5-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="a8df5-108">Por motivos de conformidade.</span><span class="sxs-lookup"><span data-stu-id="a8df5-108">For compliance reasons.</span></span>

<span data-ttu-id="a8df5-109">Para esses cenários, o sistema de proteção de dados oferece uma API de configuração avançada.</span><span class="sxs-lookup"><span data-stu-id="a8df5-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="a8df5-110">Semelhante aos arquivos de configuração, o anel da chave de proteção de dados deve ser protegido usando as permissões apropriadas.</span><span class="sxs-lookup"><span data-stu-id="a8df5-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="a8df5-111">Você pode optar por criptografar as chaves em repouso, mas isso não impede que os invasores criem novas chaves.</span><span class="sxs-lookup"><span data-stu-id="a8df5-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="a8df5-112">Consequentemente, a segurança do seu aplicativo é afetada.</span><span class="sxs-lookup"><span data-stu-id="a8df5-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="a8df5-113">O local de armazenamento configurado com proteção de dados deve ter seu acesso limitado ao aplicativo em si, semelhante ao modo como você protegeria os arquivos de configuração.</span><span class="sxs-lookup"><span data-stu-id="a8df5-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="a8df5-114">Por exemplo, se você optar por armazenar o anel de chave no disco, use as permissões do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="a8df5-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="a8df5-115">Certifique-se de que apenas a identidade sob a qual seu aplicativo Web é executado tenha acesso de leitura, gravação e criação para esse diretório.</span><span class="sxs-lookup"><span data-stu-id="a8df5-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="a8df5-116">Se você usar o armazenamento de BLOBs do Azure, somente o aplicativo Web deverá ter a capacidade de ler, gravar ou criar novas entradas no repositório de BLOB, etc.</span><span class="sxs-lookup"><span data-stu-id="a8df5-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="a8df5-117">O método de extensão [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) retorna um [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a8df5-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="a8df5-118">`IDataProtectionBuilder` expõe métodos de extensão que você pode encadear para configurar opções de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="a8df5-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="a8df5-119">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="a8df5-119">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="a8df5-120">Para armazenar chaves no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure o sistema com [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="a8df5-120">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="a8df5-121">Defina o local de armazenamento do anel de chave (por exemplo, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="a8df5-121">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="a8df5-122">O local deve ser definido porque a chamada a `ProtectKeysWithAzureKeyVault` implementa um [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) que desabilita as configurações automáticas de proteção de dados, incluindo o local de armazenamento do anel de chave.</span><span class="sxs-lookup"><span data-stu-id="a8df5-122">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="a8df5-123">O exemplo anterior usa o armazenamento de BLOBs do Azure para persistir o anel de chave.</span><span class="sxs-lookup"><span data-stu-id="a8df5-123">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="a8df5-124">Para obter mais informações, consulte provedores de armazenamento de @no__t 0Key: Armazenamento do Azure](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="a8df5-124">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="a8df5-125">Você também pode persistir o anel de chave localmente com [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="a8df5-125">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="a8df5-126">O `keyIdentifier` é o identificador de chave do Key Vault usado para criptografia de chave.</span><span class="sxs-lookup"><span data-stu-id="a8df5-126">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="a8df5-127">Por exemplo, uma chave criada no cofre de chaves denominada `dataprotection` no `contosokeyvault` tem o identificador de chave `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span><span class="sxs-lookup"><span data-stu-id="a8df5-127">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="a8df5-128">Forneça ao aplicativo a **chave de desencapsulamento** e as permissões de **chave de encapsulamento** para o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="a8df5-128">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="a8df5-129">sobrecargas `ProtectKeysWithAzureKeyVault`:</span><span class="sxs-lookup"><span data-stu-id="a8df5-129">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="a8df5-130">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permite o uso de um [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) para permitir que o sistema de proteção de dados use o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="a8df5-130">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="a8df5-131">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permite o uso de um `ClientId` e [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para permitir que o sistema de proteção de dados use o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="a8df5-131">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="a8df5-132">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permite o uso de um `ClientId` e `ClientSecret` para permitir que o sistema de proteção de dados use o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="a8df5-132">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="a8df5-133">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="a8df5-133">PersistKeysToFileSystem</span></span>

<span data-ttu-id="a8df5-134">Para armazenar chaves em um compartilhamento UNC em vez de no local padrão *% LocalAppData%* , configure o sistema com [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="a8df5-134">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="a8df5-135">Se você alterar o local de persistência da chave, o sistema não criptografará mais automaticamente as chaves em repouso, pois não sabe se o DPAPI é um mecanismo de criptografia apropriado.</span><span class="sxs-lookup"><span data-stu-id="a8df5-135">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="a8df5-136">ProtectKeysWith @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="a8df5-136">ProtectKeysWith\*</span></span>

<span data-ttu-id="a8df5-137">Você pode configurar o sistema para proteger as chaves em repouso chamando qualquer uma das APIs de configuração [ProtectKeysWith @ no__t-1](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) .</span><span class="sxs-lookup"><span data-stu-id="a8df5-137">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="a8df5-138">Considere o exemplo a seguir, que armazena chaves em um compartilhamento UNC e criptografa essas chaves em repouso com um certificado X. 509 específico:</span><span class="sxs-lookup"><span data-stu-id="a8df5-138">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a8df5-139">No ASP.NET Core 2,1 ou posterior, você pode fornecer um [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), como um certificado carregado de um arquivo:</span><span class="sxs-lookup"><span data-stu-id="a8df5-139">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="a8df5-140">Consulte [criptografia de chave em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais exemplos e discussão sobre os mecanismos de criptografia de chave internos.</span><span class="sxs-lookup"><span data-stu-id="a8df5-140">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="a8df5-141">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="a8df5-141">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="a8df5-142">No ASP.NET Core 2,1 ou posterior, você pode girar certificados e descriptografar chaves em repouso usando uma matriz de certificados [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) com [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="a8df5-142">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="a8df5-143">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="a8df5-143">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="a8df5-144">Para configurar o sistema para usar um tempo de vida de chave de 14 dias em vez do padrão de 90 dias, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="a8df5-144">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="a8df5-145">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="a8df5-145">SetApplicationName</span></span>

<span data-ttu-id="a8df5-146">Por padrão, o sistema de proteção de dados isola os aplicativos uns dos outros com base em seus caminhos de [raiz de conteúdo](xref:fundamentals/index#content-root) , mesmo se eles estiverem compartilhando o mesmo repositório de chave física.</span><span class="sxs-lookup"><span data-stu-id="a8df5-146">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="a8df5-147">Isso impede que os aplicativos compreendam os conteúdos protegidos uns dos outros.</span><span class="sxs-lookup"><span data-stu-id="a8df5-147">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="a8df5-148">Para compartilhar cargas protegidas entre aplicativos:</span><span class="sxs-lookup"><span data-stu-id="a8df5-148">To share protected payloads among apps:</span></span>

* <span data-ttu-id="a8df5-149">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> em cada aplicativo com o mesmo valor.</span><span class="sxs-lookup"><span data-stu-id="a8df5-149">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="a8df5-150">Use a mesma versão da pilha de API de proteção de dados entre os aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a8df5-150">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="a8df5-151">Execute **uma** das seguintes opções nos arquivos de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a8df5-151">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="a8df5-152">Referencie a mesma versão de estrutura compartilhada por meio do [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a8df5-152">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="a8df5-153">Referencie a mesma versão do [pacote de proteção de dados](xref:security/data-protection/introduction#package-layout) .</span><span class="sxs-lookup"><span data-stu-id="a8df5-153">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="a8df5-154">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="a8df5-154">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="a8df5-155">Você pode ter um cenário em que não deseja que um aplicativo faça o roll-Keys automaticamente (criar novas chaves) enquanto eles se aproximam da expiração.</span><span class="sxs-lookup"><span data-stu-id="a8df5-155">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="a8df5-156">Um exemplo disso pode ser os aplicativos configurados em uma relação primária/secundária, em que somente o aplicativo primário é responsável por questões de gerenciamento de chaves e os aplicativos secundários simplesmente têm uma exibição somente leitura do anel de chave.</span><span class="sxs-lookup"><span data-stu-id="a8df5-156">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="a8df5-157">Os aplicativos secundários podem ser configurados para tratar o anel de chave como somente leitura, configurando o sistema com <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span><span class="sxs-lookup"><span data-stu-id="a8df5-157">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="a8df5-158">Isolamento por aplicativo</span><span class="sxs-lookup"><span data-stu-id="a8df5-158">Per-application isolation</span></span>

<span data-ttu-id="a8df5-159">Quando o sistema de proteção de dados é fornecido por um host ASP.NET Core, ele isola automaticamente os aplicativos uns dos outros, mesmo se esses aplicativos estiverem em execução na mesma conta de processo de trabalho e estiverem usando o mesmo material de chave mestra.</span><span class="sxs-lookup"><span data-stu-id="a8df5-159">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="a8df5-160">Isso é um pouco semelhante ao modificador IsolateApps do elemento `<machineKey>` do System. Web.</span><span class="sxs-lookup"><span data-stu-id="a8df5-160">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="a8df5-161">O mecanismo de isolamento funciona considerando cada aplicativo no computador local como um locatário exclusivo, portanto, o <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> com raiz para qualquer aplicativo específico inclui automaticamente a ID do aplicativo como um discriminador.</span><span class="sxs-lookup"><span data-stu-id="a8df5-161">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="a8df5-162">A ID exclusiva do aplicativo é o caminho físico do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a8df5-162">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="a8df5-163">Para aplicativos hospedados no IIS, a ID exclusiva é o caminho físico do IIS do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8df5-163">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="a8df5-164">Se um aplicativo for implantado em um ambiente de web farm, esse valor será estável supondo que os ambientes IIS sejam configurados de forma semelhante em todos os computadores da web farm.</span><span class="sxs-lookup"><span data-stu-id="a8df5-164">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="a8df5-165">Para aplicativos hospedados internamente em execução no [servidor Kestrel](xref:fundamentals/servers/index#kestrel), a ID exclusiva é o caminho físico para o aplicativo no disco.</span><span class="sxs-lookup"><span data-stu-id="a8df5-165">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="a8df5-166">O identificador exclusivo é projetado para sobreviver à redefinição de @ no__t-0both do aplicativo individual e da própria máquina.</span><span class="sxs-lookup"><span data-stu-id="a8df5-166">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="a8df5-167">Esse mecanismo de isolamento pressupõe que os aplicativos não são mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="a8df5-167">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="a8df5-168">Um aplicativo mal-intencionado sempre pode afetar qualquer outro aplicativo em execução na mesma conta de processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="a8df5-168">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="a8df5-169">Em um ambiente de hospedagem compartilhado em que os aplicativos são mutuamente não confiáveis, o provedor de hospedagem deve executar etapas para garantir o isolamento no nível do sistema operacional entre aplicativos, incluindo a separação dos repositórios de chave subjacente dos aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a8df5-169">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="a8df5-170">Se o sistema de proteção de dados não for fornecido por um host ASP.NET Core (por exemplo, se você instanciá-lo por meio do tipo concreto `DataProtectionProvider`), o isolamento de aplicativo será desabilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="a8df5-170">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="a8df5-171">Quando o isolamento de aplicativo é desabilitado, todos os aplicativos apoiados pelo mesmo material de chave podem compartilhar cargas desde que forneçam as [finalidades](xref:security/data-protection/consumer-apis/purpose-strings)apropriadas.</span><span class="sxs-lookup"><span data-stu-id="a8df5-171">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="a8df5-172">Para fornecer isolamento de aplicativo nesse ambiente, chame o método [Setapplicationname](#setapplicationname) no objeto de configuração e forneça um nome exclusivo para cada aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a8df5-172">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="a8df5-173">Alterando algoritmos com UseCryptographicAlgorithms</span><span class="sxs-lookup"><span data-stu-id="a8df5-173">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="a8df5-174">A pilha de proteção de dados permite que você altere o algoritmo padrão usado por chaves geradas recentemente.</span><span class="sxs-lookup"><span data-stu-id="a8df5-174">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="a8df5-175">A maneira mais simples de fazer isso é chamar [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) do retorno de chamada de configuração:</span><span class="sxs-lookup"><span data-stu-id="a8df5-175">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="a8df5-176">O EncryptionAlgorithm padrão é AES-256-CBC e o ValidationAlgorithm padrão é HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="a8df5-176">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="a8df5-177">A política padrão pode ser definida por um administrador de sistema por meio de uma [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy), mas uma chamada explícita para `UseCryptographicAlgorithms` substitui a política padrão.</span><span class="sxs-lookup"><span data-stu-id="a8df5-177">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="a8df5-178">Chamar `UseCryptographicAlgorithms` permite que você especifique o algoritmo desejado de uma lista interna predefinida.</span><span class="sxs-lookup"><span data-stu-id="a8df5-178">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="a8df5-179">Você não precisa se preocupar com a implementação do algoritmo.</span><span class="sxs-lookup"><span data-stu-id="a8df5-179">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="a8df5-180">No cenário acima, o sistema de proteção de dados tenta usar a implementação CNG do AES se estiver em execução no Windows.</span><span class="sxs-lookup"><span data-stu-id="a8df5-180">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="a8df5-181">Caso contrário, ele retornará à classe [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) gerenciada.</span><span class="sxs-lookup"><span data-stu-id="a8df5-181">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="a8df5-182">Você pode especificar manualmente uma implementação por meio de uma chamada para [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="a8df5-182">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="a8df5-183">A alteração de algoritmos não afeta as chaves existentes no anel de chave.</span><span class="sxs-lookup"><span data-stu-id="a8df5-183">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="a8df5-184">Afeta apenas as chaves geradas recentemente.</span><span class="sxs-lookup"><span data-stu-id="a8df5-184">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="a8df5-185">Especificando algoritmos gerenciados personalizados</span><span class="sxs-lookup"><span data-stu-id="a8df5-185">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a8df5-186">Para especificar algoritmos gerenciados personalizados, crie uma instância de [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) que aponte para os tipos de implementação:</span><span class="sxs-lookup"><span data-stu-id="a8df5-186">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="a8df5-187">Para especificar algoritmos gerenciados personalizados, crie uma instância de [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) que aponte para os tipos de implementação:</span><span class="sxs-lookup"><span data-stu-id="a8df5-187">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="a8df5-188">Geralmente, as propriedades \*Type devem apontar para concreto, instanciáveis (por meio de uma implementação de construtor público sem parâmetros) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) e [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), embora o sistema especial tenha alguns valores como `typeof(Aes)` para necessidade.</span><span class="sxs-lookup"><span data-stu-id="a8df5-188">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="a8df5-189">O SymmetricAlgorithm deve ter um comprimento de chave de ≥ 128 bits e um tamanho de bloco de ≥ 64 bits, e deve dar suporte à criptografia no modo CBC com o preenchimento de #7 PKCS.</span><span class="sxs-lookup"><span data-stu-id="a8df5-189">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="a8df5-190">O KeyedHashAlgorithm deve ter um tamanho de Resumo de > = 128 bits e deve dar suporte a chaves de comprimento igual ao tamanho de resumo do algoritmo de hash.</span><span class="sxs-lookup"><span data-stu-id="a8df5-190">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="a8df5-191">O KeyedHashAlgorithm não é estritamente necessário para ser HMAC.</span><span class="sxs-lookup"><span data-stu-id="a8df5-191">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="a8df5-192">Especificando algoritmos personalizados de CNG do Windows</span><span class="sxs-lookup"><span data-stu-id="a8df5-192">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a8df5-193">Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo CBC com a validação HMAC, crie uma instância [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) que contenha as informações do algoritmo:</span><span class="sxs-lookup"><span data-stu-id="a8df5-193">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="a8df5-194">Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo CBC com a validação HMAC, crie uma instância [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) que contenha as informações do algoritmo:</span><span class="sxs-lookup"><span data-stu-id="a8df5-194">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a8df5-195">O algoritmo de codificação de bloco simétrico deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de > = 64 bits e deve dar suporte à criptografia do modo CBC com o preenchimento de #7 PKCS.</span><span class="sxs-lookup"><span data-stu-id="a8df5-195">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="a8df5-196">O algoritmo de hash deve ter um tamanho de Resumo de > = 128 bits e deve ter suporte para ser aberto com o sinalizador BCRYPT @ no__t-0ALG @ no__t-1HANDLE @ no__t-2HMAC @ no__t-3FLAG.</span><span class="sxs-lookup"><span data-stu-id="a8df5-196">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="a8df5-197">As propriedades \*Provider podem ser definidas como NULL para usar o provedor padrão para o algoritmo especificado.</span><span class="sxs-lookup"><span data-stu-id="a8df5-197">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="a8df5-198">Consulte a documentação do [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8df5-198">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a8df5-199">Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo de Galois/contador com validação, crie uma instância [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) que contenha as informações de algoritmos:</span><span class="sxs-lookup"><span data-stu-id="a8df5-199">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="a8df5-200">Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo de Galois/contador com validação, crie uma instância [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) que contenha as informações de algoritmos:</span><span class="sxs-lookup"><span data-stu-id="a8df5-200">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a8df5-201">O algoritmo de codificação de bloco simétrico deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de exatamente 128 bits e deve dar suporte à criptografia GCM.</span><span class="sxs-lookup"><span data-stu-id="a8df5-201">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="a8df5-202">Você pode definir a propriedade [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) como NULL para usar o provedor padrão para o algoritmo especificado.</span><span class="sxs-lookup"><span data-stu-id="a8df5-202">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="a8df5-203">Consulte a documentação do [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8df5-203">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="a8df5-204">Especificando outros algoritmos personalizados</span><span class="sxs-lookup"><span data-stu-id="a8df5-204">Specifying other custom algorithms</span></span>

<span data-ttu-id="a8df5-205">Embora não sejam expostos como uma API de primeira classe, o sistema de proteção de dados é extensível o suficiente para permitir a especificação de praticamente qualquer tipo de algoritmo.</span><span class="sxs-lookup"><span data-stu-id="a8df5-205">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="a8df5-206">Por exemplo, é possível manter todas as chaves contidas em um HSM (módulo de segurança de hardware) e fornecer uma implementação personalizada das principais rotinas de criptografia e descriptografia.</span><span class="sxs-lookup"><span data-stu-id="a8df5-206">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="a8df5-207">Consulte [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) na [extensibilidade de criptografia de núcleo](xref:security/data-protection/extensibility/core-crypto) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a8df5-207">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="a8df5-208">Mantendo chaves ao hospedar em um contêiner do Docker</span><span class="sxs-lookup"><span data-stu-id="a8df5-208">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="a8df5-209">Ao hospedar em um contêiner do [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) , as chaves devem ser mantidas em:</span><span class="sxs-lookup"><span data-stu-id="a8df5-209">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="a8df5-210">Uma pasta que é um volume do Docker que persiste além do tempo de vida do contêiner, como um volume compartilhado ou um volume montado pelo host.</span><span class="sxs-lookup"><span data-stu-id="a8df5-210">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="a8df5-211">Um provedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="a8df5-211">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="a8df5-212">Mantendo chaves com Redis</span><span class="sxs-lookup"><span data-stu-id="a8df5-212">Persisting keys with Redis</span></span>

<span data-ttu-id="a8df5-213">Somente versões Redis que dão suporte à [persistência de dados Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) devem ser usadas para armazenar chaves.</span><span class="sxs-lookup"><span data-stu-id="a8df5-213">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="a8df5-214">O [armazenamento de BLOBs do Azure](/azure/storage/blobs/storage-blobs-introduction) é persistente e pode ser usado para armazenar chaves.</span><span class="sxs-lookup"><span data-stu-id="a8df5-214">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="a8df5-215">Para obter mais informações, consulte [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/13476).</span><span class="sxs-lookup"><span data-stu-id="a8df5-215">For more information, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a8df5-216">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a8df5-216">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
