---
title: Criptografia de chave em repouso no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação de criptografia de chave de proteção de dados ASP.NET Core em repouso.
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658385"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a>Criptografia de chave em repouso no ASP.NET Core

O sistema de proteção de dados [emprega um mecanismo de descoberta por padrão](xref:security/data-protection/configuration/default-settings) para determinar como as chaves criptográficas devem ser criptografadas em repouso. O desenvolvedor pode substituir o mecanismo de descoberta e especificar manualmente como as chaves devem ser criptografadas em repouso.

> [!WARNING]
> Se você especificar um [local de persistência de chave](xref:security/data-protection/implementation/key-storage-providers)explícita, o sistema de proteção de dados cancelará o registro do mecanismo de criptografia de chave padrão em repouso. Consequentemente, as chaves não são mais criptografadas em repouso. Recomendamos que você [especifique um mecanismo de criptografia de chave explícito](xref:security/data-protection/implementation/key-encryption-at-rest) para implantações de produção. As opções de mecanismo de criptografia em repouso são descritas neste tópico.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Cofre de Chave do Azure

Para armazenar chaves no [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure o sistema com [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) na classe `Startup`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Para obter mais informações, consulte [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Aplica-se somente a implantações do Windows.**

Quando o Windows DPAPI é usado, o material da chave é criptografado com [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) antes de ser persistido no armazenamento. O DPAPI é um mecanismo de criptografia apropriado para dados que nunca são lidos fora do computador atual (embora seja possível fazer o back-up dessas chaves até Active Directory; consulte [DPAPI e perfis móveis](https://support.microsoft.com/kb/309408/#6)). Para configurar a criptografia de chave em repouso DPAPI, chame um dos métodos de extensão [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Se `ProtectKeysWithDpapi` for chamado sem parâmetros, somente a conta de usuário do Windows atual poderá decifrar o anel de chave persistente. Opcionalmente, você pode especificar que qualquer conta de usuário no computador (não apenas a conta de usuário atual) seja capaz de decifrar o anel de chave:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Certificado X.509

Se o aplicativo for distribuído entre vários computadores, poderá ser conveniente distribuir um certificado X. 509 compartilhado entre os computadores e configurar os aplicativos hospedados para usar o certificado para criptografia de chaves em repouso:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Devido a limitações de .NET Framework, há suporte apenas para certificados com chaves privadas CAPI. Consulte o conteúdo abaixo para obter possíveis soluções alternativas para essas limitações.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**Esse mecanismo está disponível somente no Windows 8/Windows Server 2012 ou posterior.**

A partir do Windows 8, o sistema operacional Windows dá suporte a DPAPI-NG (também chamado de CNG DPAPI). Para obter mais informações, consulte [about CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

A entidade de segurança é codificada como uma regra de descritor de proteção. No exemplo a seguir que chama [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), somente o usuário ingressado no domínio com o SID especificado pode descriptografar o anel de chave:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Também há uma sobrecarga sem parâmetros de `ProtectKeysWithDpapiNG`. Use este método de conveniência para especificar a regra "SID = {CURRENT_ACCOUNT_SID}", em que *CURRENT_ACCOUNT_SID* é o SID da conta de usuário atual do Windows:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

Nesse cenário, o controlador de domínio do AD é responsável por distribuir as chaves de criptografia usadas pelas operações de DPAPI-NG. O usuário de destino pode decifrar a carga criptografada de qualquer computador associado ao domínio (desde que o processo esteja sendo executado sob sua identidade).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Criptografia baseada em certificado com o Windows DPAPI-NG

Se o aplicativo estiver em execução no Windows 8.1/Windows Server 2012 R2 ou posterior, você poderá usar o Windows DPAPI-NG para executar a criptografia baseada em certificado. Use a cadeia de caracteres do descritor de regra "CERTIFICATE = HashId: impressão digital", em que a *impressão digital* é a impressão digital SHA1 codificada em hex do certificado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Qualquer aplicativo apontado neste repositório deve estar em execução no Windows 8.1/Windows Server 2012 R2 ou posterior para decifrar as chaves.

## <a name="custom-key-encryption"></a>Criptografia de chave personalizada

Se os mecanismos na caixa não forem apropriados, o desenvolvedor poderá especificar seu próprio mecanismo de criptografia de chave, fornecendo um [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)personalizado.
