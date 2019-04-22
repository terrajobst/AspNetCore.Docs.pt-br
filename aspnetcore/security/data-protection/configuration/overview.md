---
title: Configurar a proteção de dados do ASP.NET Core
author: rick-anderson
description: Saiba como configurar a proteção de dados no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: ee43427fa1e82a365d49df50567b4ca7afb5a5d3
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516242"
---
# <a name="configure-aspnet-core-data-protection"></a>Configurar a proteção de dados do ASP.NET Core

Quando o sistema de proteção de dados é inicializado, ele se aplica [as configurações padrão](xref:security/data-protection/configuration/default-settings) com base no ambiente operacional. Essas configurações são geralmente apropriadas para aplicativos em execução em um único computador. Há casos em que um desenvolvedor talvez queira alterar as configurações padrão:

* O aplicativo é distribuído entre várias máquinas.
* Por motivos de conformidade.

Para esses cenários, o sistema de proteção de dados oferece uma API de configuração avançada.

> [!WARNING]
> Semelhante aos arquivos de configuração, o anel de chave de proteção de dados devem ser protegido usando as permissões apropriadas. Você pode optar por criptografar as chaves em repouso, mas isso não impede que os invasores desde a criação de novas chaves. Consequentemente, a segurança desse aplicativo é afetada. O local de armazenamento configurado com proteção de dados deve ter seu acesso limitado ao próprio aplicativo, semelhante à forma como você protegerá os arquivos de configuração. Por exemplo, se você optar por armazenar seu token de autenticação no disco, use as permissões do sistema de arquivos. Certifique-se apenas a identidade sob a qual seu aplicativo web é executado tem ler, gravar e criar acesso a esse diretório. Se você usar o armazenamento de tabelas do Azure, apenas o aplicativo web deve ter a capacidade de ler, gravar ou criar novas entradas no repositório de tabela, etc.
>
> O método de extensão [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) retorna um [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder` expõe métodos de extensão que você pode encadear configurar a proteção de dados de opções.

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Para armazenar as chaves na [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure o sistema com [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) no `Startup` classe:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Defina o local de armazenamento do anel de chave (por exemplo, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). O local deve ser definido porque chamando `ProtectKeysWithAzureKeyVault` implementa uma [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) que desabilita as configurações de proteção automática de dados, incluindo o local de armazenamento do anel de chave. O exemplo anterior usa o armazenamento de BLOBs do Azure para persistir o anel de chave. Para obter mais informações, consulte [principais provedores de armazenamento: O Azure e Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis). Também é possível persistir o token de autenticação localmente com [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

O `keyIdentifier` é o identificador de chave de Cofre de chaves usado para criptografia de chave. Por exemplo, uma chave criada no cofre de chaves chamado `dataprotection` no `contosokeyvault` tem o identificador de chave `https://contosokeyvault.vault.azure.net/keys/dataprotection/`. Forneça o aplicativo com **Unwrap Key** e **Wrap Key** permissões ao Cofre de chaves.

`ProtectKeysWithAzureKeyVault` sobrecargas:

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permite o uso de um [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) para permitir que o sistema de proteção de dados usar o Cofre de chaves.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, cadeia de caracteres, cadeia de caracteres, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permite o uso de um `ClientId` e [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) para permitir que o sistema de proteção de dados usar o Cofre de chaves.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permite o uso de um `ClientId` e `ClientSecret` para permitir que o sistema de proteção de dados usar o Cofre de chaves.

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Para armazenar chaves em um compartilhamento UNC, em vez da a *% LOCALAPPDATA %* local padrão, configure o sistema com [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Se você alterar o local de persistência de chave, o sistema não criptografa automaticamente os chaves em repouso, já que ele não sabe se o DPAPI é um mecanismo de criptografia apropriado.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Você pode configurar o sistema para proteger as chaves em repouso chamando o [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) APIs de configuração. Considere o exemplo a seguir, que armazena as chaves em um compartilhamento UNC e criptografa essas chaves em repouso com um certificado x. 509 específico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

No ASP.NET Core 2.1 ou posterior, você pode fornecer um [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) à [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), tais como carregar um certificado de um arquivo:

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

Ver [chave de criptografia em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais exemplos e uma discussão sobre os mecanismos de criptografia de chave interna.

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

No ASP.NET Core 2.1 ou posterior, você pode girar certificados e descriptografar chaves em repouso usando uma matriz de [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificados com [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

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

Para configurar o sistema para usar uma vida útil de 14 dias, em vez do padrão 90 dias, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Por padrão, o sistema de proteção de dados isola os aplicativos uns dos outros com base em seus caminhos de conteúdo raiz, mesmo que compartilham o mesmo repositório de chave físico. Isso impede que os aplicativos Noções básicas sobre cargas protegidas uns dos outros.

Compartilhar protegido cargas entre aplicativos:

* Configurar <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> em cada aplicativo com o mesmo valor.
* Use a mesma versão da pilha de API de proteção de dados entre os aplicativos. Realizar **qualquer** das seguintes opções em arquivos de projeto de aplicativos:
  * A mesma versão da estrutura compartilhada por meio de referência a [metapacote Microsoft](xref:fundamentals/metapackage-app).
  * Referência à mesma [pacote de proteção de dados](xref:security/data-protection/introduction#package-layout) versão.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Você pode ter um cenário onde você não deseja que um aplicativo para reverter automaticamente chaves (criar novas chaves), como eles abordam a expiração. Um exemplo disso pode ser configurados em uma relação primária/secundária, em que apenas o aplicativo principal é responsável por questões de gerenciamento de chaves e aplicativos secundários simplesmente tem uma exibição somente leitura do anel de chave de aplicativos. Os aplicativos secundários podem ser configurados para tratar o anel de chave como somente leitura ao configurar o sistema com <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Isolamento por aplicativo

Quando o sistema de proteção de dados é fornecido por um host do ASP.NET Core, ele automaticamente isola os aplicativos uns dos outros, mesmo que esses aplicativos estão em execução na mesma conta de processo de trabalho e estiver usando o mesmo material de chave mestra. Isso é um pouco semelhante ao modificador IsolateApps partir do System. Web `<machineKey>` elemento.

O mecanismo de isolamento funciona considerando cada aplicativo no computador local como um locatário exclusivo, assim o <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> com raiz para qualquer aplicativo em particular automaticamente inclui a ID do aplicativo como um discriminador. A ID do aplicativo exclusivo é o caminho físico do aplicativo:

* Para aplicativos hospedados no [IIS](xref:fundamentals/servers/index#iis-http-server), a ID exclusiva é o caminho físico do IIS do aplicativo. Se um aplicativo é implantado em um ambiente de farm da web, esse valor é estável, supondo que os ambientes de IIS são configurados da mesma forma em todas as máquinas no web farm.
* Para aplicativos de auto-hospedados em execução no [servidor Kestrel](xref:fundamentals/servers/index#kestrel), a ID exclusiva é o caminho físico para o aplicativo no disco.

O identificador exclusivo é projetado para sobreviver a reinicializações&mdash;do aplicativo individual e a própria máquina.

Esse mecanismo de isolamento pressupõe que os aplicativos não são mal-intencionados. Um aplicativo mal-intencionado sempre pode afetar qualquer outro aplicativo em execução sob a mesma conta de processo de trabalho. Em um ambiente de hospedagem compartilhado em que os aplicativos são mutuamente não confiáveis, o provedor de hospedagem deve tomar medidas para garantir o isolamento do nível de sistema operacional entre aplicativos, incluindo a separação de repositórios de chave de base de aplicativos.

Se o sistema de proteção de dados não é fornecido por um host do ASP.NET Core (por exemplo, se você instanciá-la por meio de `DataProtectionProvider` tipo concreto) isolamento de aplicativo está desabilitado por padrão. Quando o isolamento de aplicativo está desabilitado, todos os aplicativos de apoio com o mesmo material de chaveamento podem compartilhar cargas desde que eles fornecem apropriado [fins](xref:security/data-protection/consumer-apis/purpose-strings). Para fornecer isolamento de aplicativo nesse ambiente, chame o [SetApplicationName](#setapplicationname) método na configuração do objeto e forneça um nome exclusivo para cada aplicativo.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Alterando os algoritmos com UseCryptographicAlgorithms

A pilha da proteção de dados permite que você altere o algoritmo padrão usado por chaves recentemente geradas. A maneira mais simples de fazer isso é chamar [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) do retorno de chamada de configuração:

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

O padrão EncryptionAlgorithm é AES-256-CBC e o padrão ValidationAlgorithm é HMACSHA256. A política padrão pode ser definida por um administrador do sistema por meio de um [política de todo o computador](xref:security/data-protection/configuration/machine-wide-policy), mas uma chamada explícita para `UseCryptographicAlgorithms` substitui a política padrão.

Chamar `UseCryptographicAlgorithms` permite que você especifique o algoritmo desejado em uma lista predefinida de interna. Você não precisa se preocupar sobre a implementação do algoritmo. No cenário acima, o sistema de proteção de dados tenta usar a implementação de CNG do AES se em execução no Windows. Caso contrário, ele reverterá para o gerenciado [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) classe.

Você pode especificar manualmente uma implementação por meio de uma chamada para [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Algoritmos a alteração não afeta as chaves existentes do anel de chave. Ela afeta apenas as chaves recentemente geradas.

### <a name="specifying-custom-managed-algorithms"></a>Especificando algoritmos gerenciados personalizados

::: moniker range=">= aspnetcore-2.0"

Para especificar algoritmos gerenciados personalizados, crie uma [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instância que aponta para os tipos de implementação:

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

Para especificar algoritmos gerenciados personalizados, crie uma [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instância que aponta para os tipos de implementação:

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

Geralmente, o \*propriedades de tipo devem apontar para concreto, implementações que pode ser instanciadas (por meio de um construtor sem parâmetros público) de [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) e [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), embora o sistema classifica como caso especial, como alguns valores `typeof(Aes)` para sua conveniência.

> [!NOTE]
> O SymmetricAlgorithm deve ter um comprimento de chave de 128 bits ≥ e um tamanho de bloco de 64 bits do ≥, e ele deve oferecer suporte à criptografia de modo CBC com preenchimento PKCS #7. O KeyedHashAlgorithm deve ter um tamanho de resumo de > = 128 bits, e ele deve oferecer suporte a chaves de comprimento igual ao comprimento do resumo do algoritmo de hash. O KeyedHashAlgorithm não é estritamente necessária para ser HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Especificando algoritmos personalizados da CNG do Windows

::: moniker range=">= aspnetcore-2.0"

Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo CBC com validação do HMAC, crie uma [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instância que contém as informações de algoritmos:

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

Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo CBC com validação do HMAC, crie uma [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instância que contém as informações de algoritmos:

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
> O algoritmo de criptografia de bloco simétricas deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de > = 64 bits, e ele deve oferecer suporte à criptografia de modo CBC com preenchimento PKCS #7. O algoritmo de hash deve ter um tamanho de resumo de > = 128 bits e deve oferecer suporte a que está sendo aberto com o BCRYPT\_ALG\_MANIPULAR\_HMAC\_sinalizador de sinalizador. O \*propriedades do provedor podem ser definidas como nulas para usar o provedor padrão para o algoritmo especificado. Consulte a [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentação para obter mais informações.

::: moniker range=">= aspnetcore-2.0"

Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo Galois/contador com a validação, crie uma [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instância que contém as informações de algoritmos:

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

Para especificar um algoritmo personalizado do CNG do Windows usando a criptografia de modo Galois/contador com a validação, crie uma [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instância que contém as informações de algoritmos:

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
> O algoritmo de criptografia de bloco simétricas deve ter um comprimento de chave de > = 128 bits, um tamanho de bloco de exatamente de 128 bits, e ele deve oferecer suporte a criptografia do GCM. Você pode definir as [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) propriedade como nulo para usar o provedor padrão para o algoritmo especificado. Consulte a [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentação para obter mais informações.

### <a name="specifying-other-custom-algorithms"></a>Especificar outros algoritmos personalizados

Embora não é exposta como uma API de primeira classe, o sistema de proteção de dados é extensível o suficiente para permitir a especificação de praticamente qualquer tipo de algoritmo. Por exemplo, é possível manter todas as chaves contidas dentro do módulo de segurança um Hardware (HSM) e fornecer uma implementação personalizada do núcleo rotinas de criptografia e descriptografia. Ver [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) na [principais de extensibilidade da criptografia](xref:security/data-protection/extensibility/core-crypto) para obter mais informações.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Chaves persistentes ao hospedar em um contêiner do Docker

Ao hospedar em um [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) contêiner, as chaves devem ser mantidas em qualquer um:

* Uma pasta que é um volume do Docker que persiste além do tempo de vida do contêiner, como um volume compartilhado ou um volume montado pelo host.
* Um provedor externo, como [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou [Redis](https://redis.io/).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
