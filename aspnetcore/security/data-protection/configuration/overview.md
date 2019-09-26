---
title: Configurar a proteção de dados do ASP.NET Core
author: rick-anderson
description: Saiba como configurar a proteção de dados no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 8774c1a046ef85492bfcdcb0d65c827b8708425d
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278739"
---
# <a name="configure-aspnet-core-data-protection"></a>Configurar a proteção de dados do ASP.NET Core

Quando o sistema de proteção de dados é inicializado, ele aplica [as configurações padrão](xref:security/data-protection/configuration/default-settings) com base no ambiente operacional. Essas configurações geralmente são apropriadas para aplicativos em execução em um único computador. Há casos em que um desenvolvedor pode querer alterar as configurações padrão:

* O aplicativo é distribuído entre vários computadores.
* Por motivos de conformidade.

Para esses cenários, o sistema de proteção de dados oferece uma API de configuração avançada.

> [!WARNING]
> Semelhante aos arquivos de configuração, o anel da chave de proteção de dados deve ser protegido usando as permissões apropriadas. Você pode optar por criptografar as chaves em repouso, mas isso não impede que os invasores criem novas chaves. Consequentemente, a segurança do seu aplicativo é afetada. O local de armazenamento configurado com proteção de dados deve ter seu acesso limitado ao aplicativo em si, semelhante ao modo como você protegeria os arquivos de configuração. Por exemplo, se você optar por armazenar o anel de chave no disco, use as permissões do sistema de arquivos. Certifique-se de que apenas a identidade sob a qual seu aplicativo Web é executado tenha acesso de leitura, gravação e criação para esse diretório. Se você usar o armazenamento de BLOBs do Azure, somente o aplicativo Web deverá ter a capacidade de ler, gravar ou criar novas entradas no repositório de BLOB, etc.
>
> O método de extensão [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) retorna um [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder`expõe métodos de extensão que você pode encadear para configurar opções de proteção de dados.

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Para armazenar chaves no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure o sistema com [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) na `Startup` classe:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Defina o local de armazenamento do anel de chave (por exemplo, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). O local deve ser definido porque a `ProtectKeysWithAzureKeyVault` chamada implementa um [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) que desabilita as configurações automáticas de proteção de dados, incluindo o local de armazenamento do anel de chaves. O exemplo anterior usa o armazenamento de BLOBs do Azure para persistir o anel de chave. Para obter mais informações, [consulte provedores de armazenamento de chaves: Armazenamento do Azure](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Você também pode persistir o anel de chave localmente com [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

O `keyIdentifier` é o identificador de chave do Key Vault usado para criptografia de chave. Por exemplo, uma chave criada no Key Vault chamada `dataprotection` `contosokeyvault` no tem o identificador `https://contosokeyvault.vault.azure.net/keys/dataprotection/`de chave. Forneça ao aplicativo a **chave de desencapsulamento** e as permissões de **chave de encapsulamento** para o cofre de chaves.

`ProtectKeysWithAzureKeyVault`sobrecargas

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permite o uso de um [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) para permitir que o sistema de proteção de dados use o cofre de chaves.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permite o uso de a `ClientId` e [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para permitir que o sistema de proteção de dados use o cofre de chaves.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permite o uso de um `ClientId` e `ClientSecret` para permitir que o sistema de proteção de dados use o cofre de chaves.

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Para armazenar chaves em um compartilhamento UNC em vez de no local padrão *% LocalAppData%* , configure o sistema com [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Se você alterar o local de persistência da chave, o sistema não criptografará mais automaticamente as chaves em repouso, pois não sabe se o DPAPI é um mecanismo de criptografia apropriado.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Você pode configurar o sistema para proteger as chaves em repouso chamando qualquer uma das APIs de configuração do [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) . Considere o exemplo a seguir, que armazena chaves em um compartilhamento UNC e criptografa essas chaves em repouso com um certificado X. 509 específico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

No ASP.NET Core 2,1 ou posterior, você pode fornecer um [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), como um certificado carregado de um arquivo:

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

Consulte [criptografia de chave em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais exemplos e discussão sobre os mecanismos de criptografia de chave internos.

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

No ASP.NET Core 2,1 ou posterior, você pode girar certificados e descriptografar chaves em repouso usando uma matriz de certificados [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) com [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

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

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Para configurar o sistema para usar um tempo de vida de chave de 14 dias em vez do padrão de 90 dias, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Por padrão, o sistema de proteção de dados isola os aplicativos uns dos outros com base em seus caminhos de raiz de conteúdo, mesmo se eles estiverem compartilhando o mesmo repositório de chave física. Isso impede que os aplicativos compreendam os conteúdos protegidos uns dos outros.

Para compartilhar cargas protegidas entre aplicativos:

* Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> em cada aplicativo com o mesmo valor.
* Use a mesma versão da pilha de API de proteção de dados entre os aplicativos. Execute **uma** das seguintes opções nos arquivos de projeto do aplicativo:
  * Referencie a mesma versão de estrutura compartilhada por meio do [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * Referencie a mesma versão do [pacote de proteção de dados](xref:security/data-protection/introduction#package-layout) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Você pode ter um cenário em que não deseja que um aplicativo faça o roll-Keys automaticamente (criar novas chaves) enquanto eles se aproximam da expiração. Um exemplo disso pode ser os aplicativos configurados em uma relação primária/secundária, em que somente o aplicativo primário é responsável por questões de gerenciamento de chaves e os aplicativos secundários simplesmente têm uma exibição somente leitura do anel de chave. Os aplicativos secundários podem ser configurados para tratar o anel de chave como somente leitura, configurando o sistema com <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Isolamento por aplicativo

Quando o sistema de proteção de dados é fornecido por um host ASP.NET Core, ele isola automaticamente os aplicativos uns dos outros, mesmo se esses aplicativos estiverem em execução na mesma conta de processo de trabalho e estiverem usando o mesmo material de chave mestra. Isso é um pouco semelhante ao modificador IsolateApps do `<machineKey>` elemento System. Web.

O mecanismo de isolamento funciona considerando cada aplicativo no computador local como um locatário exclusivo, portanto, a <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> raiz para qualquer aplicativo específico inclui automaticamente a ID do aplicativo como um discriminador. A ID exclusiva do aplicativo é o caminho físico do aplicativo:

* Para aplicativos hospedados no IIS, a ID exclusiva é o caminho físico do IIS do aplicativo. Se um aplicativo for implantado em um ambiente de web farm, esse valor será estável supondo que os ambientes IIS sejam configurados de forma semelhante em todos os computadores da web farm.
* Para aplicativos hospedados internamente em execução no [servidor Kestrel](xref:fundamentals/servers/index#kestrel), a ID exclusiva é o caminho físico para o aplicativo no disco.

O identificador exclusivo é projetado para sobreviver redefine&mdash;o aplicativo individual e o próprio computador.

Esse mecanismo de isolamento pressupõe que os aplicativos não são mal-intencionados. Um aplicativo mal-intencionado sempre pode afetar qualquer outro aplicativo em execução na mesma conta de processo de trabalho. Em um ambiente de hospedagem compartilhado em que os aplicativos são mutuamente não confiáveis, o provedor de hospedagem deve executar etapas para garantir o isolamento no nível do sistema operacional entre aplicativos, incluindo a separação dos repositórios de chave subjacente dos aplicativos.

Se o sistema de proteção de dados não for fornecido por um host ASP.NET Core (por exemplo, se você instanciá-lo por meio do tipo concreto), o isolamento de `DataProtectionProvider` aplicativo será desabilitado por padrão. Quando o isolamento de aplicativo é desabilitado, todos os aplicativos apoiados pelo mesmo material de chave podem compartilhar cargas desde que forneçam as [finalidades](xref:security/data-protection/consumer-apis/purpose-strings)apropriadas. Para fornecer isolamento de aplicativo nesse ambiente, chame o método [Setapplicationname](#setapplicationname) no objeto de configuração e forneça um nome exclusivo para cada aplicativo.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Alterando algoritmos com UseCryptographicAlgorithms

A pilha de proteção de dados permite que você altere o algoritmo padrão usado por chaves geradas recentemente. A maneira mais simples de fazer isso é chamar [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) do retorno de chamada de configuração:

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

O EncryptionAlgorithm padrão é AES-256-CBC e o ValidationAlgorithm padrão é HMACSHA256. A política padrão pode ser definida por um administrador de sistema por meio de uma [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy), mas `UseCryptographicAlgorithms` uma chamada explícita para substitui a política padrão.

A `UseCryptographicAlgorithms` chamada permite que você especifique o algoritmo desejado de uma lista interna predefinida. Você não precisa se preocupar com a implementação do algoritmo. No cenário acima, o sistema de proteção de dados tenta usar a implementação CNG do AES se estiver em execução no Windows. Caso contrário, ele retornará à classe [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) gerenciada.

Você pode especificar manualmente uma implementação por meio de uma chamada para [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> A alteração de algoritmos não afeta as chaves existentes no anel de chave. Afeta apenas as chaves geradas recentemente.

### <a name="specifying-custom-managed-algorithms"></a>Especificando algoritmos gerenciados personalizados

::: moniker range=">= aspnetcore-2.0"

Para especificar algoritmos gerenciados personalizados, crie uma instância de [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) que aponte para os tipos de implementação:

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

Para especificar algoritmos gerenciados personalizados, crie uma instância de [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) que aponte para os tipos de implementação:

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

Geralmente, \*as propriedades de tipo devem apontar para concreto, instanciáveis (por meio de uma implementação de construtor público sem parâmetros) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) e [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), embora o sistema especial tenha alguns valores como `typeof(Aes)` para sua conveniência.

> [!NOTE]
> O SymmetricAlgorithm deve ter um comprimento de chave de ≥ 128 bits e um tamanho de bloco de ≥ 64 bits, e deve dar suporte à criptografia no modo CBC com o preenchimento de #7 PKCS. O KeyedHashAlgorithm deve ter um tamanho de Resumo de > = 128 bits e deve dar suporte a chaves de comprimento igual ao tamanho de resumo do algoritmo de hash. O KeyedHashAlgorithm não é estritamente necessário para ser HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Especificando algoritmos personalizados de CNG do Windows

::: moniker range=">= aspnetcore-2.0"

Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo CBC com a validação HMAC, crie uma instância [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) que contenha as informações do algoritmo:

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

Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo CBC com a validação HMAC, crie uma instância [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) que contenha as informações do algoritmo:

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
> O algoritmo de codificação de bloco simétrico deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de > = 64 bits e deve dar suporte à criptografia do modo CBC com o preenchimento de #7 PKCS. O algoritmo de hash deve ter um tamanho de Resumo de > = 128 bits e deve ter suporte para ser\_aberto com\_o\_sinalizador de sinalizador HMAC de manipulação de BCRYPT alg\_. As \*Propriedades do provedor podem ser definidas como NULL para usar o provedor padrão para o algoritmo especificado. Consulte a documentação do [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) para obter mais informações.

::: moniker range=">= aspnetcore-2.0"

Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo de Galois/contador com validação, crie uma instância [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) que contenha as informações de algoritmos:

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

Para especificar um algoritmo CNG personalizado do Windows usando a criptografia do modo de Galois/contador com validação, crie uma instância [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) que contenha as informações de algoritmos:

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
> O algoritmo de codificação de bloco simétrico deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de exatamente 128 bits e deve dar suporte à criptografia GCM. Você pode definir a propriedade [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) como NULL para usar o provedor padrão para o algoritmo especificado. Consulte a documentação do [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) para obter mais informações.

### <a name="specifying-other-custom-algorithms"></a>Especificando outros algoritmos personalizados

Embora não sejam expostos como uma API de primeira classe, o sistema de proteção de dados é extensível o suficiente para permitir a especificação de praticamente qualquer tipo de algoritmo. Por exemplo, é possível manter todas as chaves contidas em um HSM (módulo de segurança de hardware) e fornecer uma implementação personalizada das principais rotinas de criptografia e descriptografia. Consulte [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) na [extensibilidade de criptografia de núcleo](xref:security/data-protection/extensibility/core-crypto) para obter mais informações.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Mantendo chaves ao hospedar em um contêiner do Docker

Ao hospedar em um contêiner do [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) , as chaves devem ser mantidas em:

* Uma pasta que é um volume do Docker que persiste além do tempo de vida do contêiner, como um volume compartilhado ou um volume montado pelo host.
* Um provedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [Redis](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Mantendo chaves com Redis

Somente versões Redis que dão suporte à [persistência de dados Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) devem ser usadas para armazenar chaves. O [armazenamento de BLOBs do Azure](/azure/storage/blobs/storage-blobs-introduction) é persistente e pode ser usado para armazenar chaves. Para obter mais informações, consulte [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/13476).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
