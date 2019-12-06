---
title: Provedores de armazenamento de chaves no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre provedores de armazenamento de chaves no ASP.NET Core e como configurar locais de armazenamento de chaves.
ms.author: riande
ms.date: 12/05/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 76f224fb7d322722c3875b734127da4da294a646
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881213"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Provedores de armazenamento de chaves no ASP.NET Core

O sistema de proteção de dados [emprega um mecanismo de descoberta por padrão](xref:security/data-protection/configuration/default-settings) para determinar onde as chaves de criptografia devem ser persistente. O desenvolvedor pode substituir o mecanismo de descoberta padrão e especificar manualmente o local.

> [!WARNING]
> Se você especificar um local de persistência de chave explícita, o sistema de proteção de dados cancela o registro a criptografia de chave padrão no mecanismo de rest, portanto, as chaves não são criptografadas em repouso. É recomendável que você adicionalmente [especificar um mecanismo de criptografia de chave explícita](xref:security/data-protection/implementation/key-encryption-at-rest) para implantações de produção.

## <a name="file-system"></a>Sistema de arquivos

Para configurar um repositório chave baseadas no sistema de arquivos, chame o [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) rotina de configuração, conforme mostrado abaixo. Fornecer um [DirectoryInfo](/dotnet/api/system.io.directoryinfo) apontando para o repositório em que as chaves devem ser armazenadas:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

O `DirectoryInfo` pode apontar para um diretório no computador local, ou ele pode apontar para uma pasta em um compartilhamento de rede. Se apontando para um diretório no computador local (e o cenário é que apenas os aplicativos no computador local exigem acesso para usar esse repositório), considere o uso de [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (no Windows) para criptografar as chaves em repouso. Caso contrário, considere o uso de um [certificado x. 509](xref:security/data-protection/implementation/key-encryption-at-rest) para criptografar as chaves em repouso.

## <a name="azure-storage"></a>Armazenamento do Azure

O pacote [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) permite armazenar chaves de proteção de dados no armazenamento de BLOBs do Azure. As chaves podem ser compartilhadas entre várias instâncias de um aplicativo web. Aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.

Para configurar o provedor de armazenamento de BLOBs do Azure, chame uma das sobrecargas de [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Se o aplicativo Web estiver sendo executado como um serviço do Azure, os tokens de autenticação poderão ser criados automaticamente usando [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

Veja [mais detalhes sobre como configurar a autenticação de serviço a serviço.](/azure/key-vault/service-to-service-authentication)

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

O pacote [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) permite armazenar chaves de proteção de dados em um cache Redis. As chaves podem ser compartilhadas entre várias instâncias de um aplicativo web. Aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O pacote [Microsoft. AspNetCore. dataprotection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) permite armazenar chaves de proteção de dados em um cache Redis. As chaves podem ser compartilhadas entre várias instâncias de um aplicativo web. Aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Para configurar o Redis, chame um dos [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) sobrecargas:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Para configurar o Redis, chame um dos [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) sobrecargas:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Para mais informações, consulte os seguintes tópicos:

* [ConnectionMultiplexer stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Cache Redis do Azure](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [exemplos de ASPNET/DataProtection](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Registro

**Só se aplica às implantações do Windows.**

Às vezes, o aplicativo pode não ter acesso de gravação para o sistema de arquivos. Considere um cenário em que um aplicativo é executado como uma conta de serviço virtual (como *w3wp.exe*da identidade do pool de aplicativo). Nesses casos, o administrador pode provisionar uma chave do registro que seja acessível pela identidade da conta de serviço. Chame o [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) método de extensão, conforme mostrado abaixo. Fornecer um [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) apontando para o local onde as chaves de criptografia devem ser armazenadas:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> É recomendável usar [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) para criptografar as chaves em repouso.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

O [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) pacote fornece um mecanismo para armazenar chaves de proteção de dados para um banco de dados usando o Entity Framework Core. O `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` pacote do NuGet deve ser adicionado ao arquivo de projeto, ele não é parte do [metapacote Microsoft](xref:fundamentals/metapackage-app).

Com esse pacote, as chaves podem ser compartilhadas entre várias instâncias de um aplicativo web.

Para configurar o provedor de EF Core, chame o método [PersistKeysToDbContext\<TContext >](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

O parâmetro genérico, `TContext`, deve herdar de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e implementar [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Crie a tabela `DataProtectionKeys`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Execute os seguintes comandos na janela do **console do Gerenciador de pacotes** (PMC):

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Execute os seguintes comandos em um shell de comando:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` é a `DbContext` definida no exemplo de código anterior. Se você estiver usando um `DbContext` com um nome diferente, substitua o nome do `DbContext` por `MyKeysContext`.

A classe/entidade `DataProtectionKeys` adota a estrutura mostrada na tabela a seguir.

| Propriedade/campo | Tipo CLR | Tipo SQL              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, CP, não nulo   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, nulo |
| `Xml`          | `string` | `nvarchar(MAX)`, nulo |

::: moniker-end

## <a name="custom-key-repository"></a>Repositório de chave personalizado

Se os mecanismos de caixa de entrada não forem apropriados, o desenvolvedor pode especificar seu próprio mecanismo de persistência de chave, fornecendo um personalizado [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).
