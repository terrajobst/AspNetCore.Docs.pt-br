---
title: Chave de criptografia em repouso no ASP.NET Core
author: rick-anderson
description: Saiba os detalhes da implementação de criptografia de chave de proteção de dados do ASP.NET Core em repouso.
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892303"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a>Chave de criptografia em repouso no ASP.NET Core

O sistema de proteção de dados [emprega um mecanismo de descoberta por padrão](xref:security/data-protection/configuration/default-settings) para determinar as chaves de criptografia como devem ser criptografados em repouso. O desenvolvedor pode substituir o mecanismo de descoberta e especificar manualmente como chaves devem ser criptografadas em repouso.

> [!WARNING]
> Se você especificar um explícito [local de persistência da chave](xref:security/data-protection/implementation/key-storage-providers), o sistema de proteção de dados cancela o registro a criptografia de chave padrão no mecanismo de rest. Consequentemente, as chaves não são criptografadas em repouso. É recomendável que você [especificar um mecanismo de criptografia de chave explícita](xref:security/data-protection/implementation/key-encryption-at-rest) para implantações de produção. As opções de mecanismo de criptografia em repouso são descritas neste tópico.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Para armazenar as chaves na [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure o sistema com [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) no `Startup` classe:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Para obter mais informações, consulte [configurar a proteção de dados do ASP.NET Core: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Só se aplica às implantações do Windows.**

Quando o Windows DPAPI é usado, o material da chave é criptografada com [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) antes que estão sendo mantidos no armazenamento. A DPAPI é um mecanismo de criptografia apropriado para os dados que nunca é lida de fora do computador atual (no entanto é possível fazer essas chaves até do Active Directory; Consulte [DPAPI e perfis móveis](https://support.microsoft.com/kb/309408/#6)). Para configurar a criptografia de chave em repouso DPAPI, chame um dos [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) métodos de extensão:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Se `ProtectKeysWithDpapi` é chamado sem parâmetros, somente a conta de usuário atual do Windows poderá decifrar o anel de chave persistente. Opcionalmente, você pode especificar que qualquer conta de usuário no computador (não apenas a conta de usuário atual) poderá decifrar o anel de chave:

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

Se o aplicativo é distribuído entre vários computadores, pode ser conveniente distribuir um certificado x. 509 compartilhado entre as máquinas e configure os aplicativos hospedados para usar o certificado de criptografia de chaves em repouso:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Devido às limitações do .NET Framework, somente os certificados com chaves privadas de CAPI têm suporte. Consulte o conteúdo abaixo para obter possíveis soluções para essas limitações.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**Esse mecanismo está disponível apenas no Windows 8/Windows Server 2012 ou posterior.**

Começando com o Windows 8, o sistema operacional do Windows oferece suporte a DPAPI-NG (também chamado de CNG DPAPI). Para obter mais informações, consulte [sobre o DPAPI CNG](/windows/desktop/SecCNG/cng-dpapi).

A entidade de segurança é codificada como uma regra de proteção do descritor. No exemplo a seguir que chama [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), somente o usuário de domínio com o SID especificado pode descriptografar o anel de chave:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Também há uma sobrecarga sem parâmetros de `ProtectKeysWithDpapiNG`. Use esse método de conveniência para especificar a regra "SID = {CURRENT_ACCOUNT_SID}", onde *CURRENT_ACCOUNT_SID* é o SID da conta de usuário atual do Windows:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

Nesse cenário, o controlador de domínio do AD é responsável por distribuir as chaves de criptografia usadas pelas operações NG DPAPI. O usuário de destino poderá decifrar a carga criptografada de qualquer computador ingressado no domínio (desde que o processo é executado sob sua identidade).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Criptografia baseada em certificado com o Windows DPAPI-NG

Se o aplicativo está em execução no Windows 8.1 / Windows Server 2012 R2 ou posterior, você pode usar o Windows DPAPI-NG para executar a criptografia baseada em certificado. Use a cadeia de caracteres do descritor de regra "certificado = HashId:THUMBPRINT", onde *impressão digital* é a impressão digital codificação hexadecimal SHA1 do certificado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Qualquer aplicativo apontado para esse repositório deve estar executando no Windows 8.1 / Windows Server 2012 R2 ou posterior para decifrar as chaves.

## <a name="custom-key-encryption"></a>Criptografia de chave personalizada

Se os mecanismos de caixa de entrada não forem apropriados, o desenvolvedor pode especificar seu próprio mecanismo de criptografia de chave, fornecendo um personalizado [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).
