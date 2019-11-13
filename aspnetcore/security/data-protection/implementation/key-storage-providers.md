---
title: Provedores de armazenamento de chaves no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre os principais provedores de armazenamento no ASP.NET Core e como configurar locais de armazenamento de chaves.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: ec746f383c18ccc7b60c614c990f7577d2d52a20
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74052839"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="3011b-103">Provedores de armazenamento de chaves no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3011b-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="3011b-104">O sistema de proteção de dados [emprega um mecanismo de descoberta por padrão](xref:security/data-protection/configuration/default-settings) para determinar onde as chaves de criptografia devem ser persistentes.</span><span class="sxs-lookup"><span data-stu-id="3011b-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="3011b-105">O desenvolvedor pode substituir o mecanismo de descoberta padrão e especificar manualmente o local.</span><span class="sxs-lookup"><span data-stu-id="3011b-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="3011b-106">Se você especificar um local de persistência de chave explícita, o sistema de proteção de dados cancelará o registro do mecanismo de criptografia de chave padrão em repouso, de modo que as chaves não sejam mais criptografadas em repouso.</span><span class="sxs-lookup"><span data-stu-id="3011b-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="3011b-107">É recomendável que você [especifique também um mecanismo de criptografia de chave explícito](xref:security/data-protection/implementation/key-encryption-at-rest) para implantações de produção.</span><span class="sxs-lookup"><span data-stu-id="3011b-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="3011b-108">Sistema de arquivos</span><span class="sxs-lookup"><span data-stu-id="3011b-108">File system</span></span>

<span data-ttu-id="3011b-109">Para configurar um repositório de chaves baseado em sistema de arquivos, chame a rotina de configuração do [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) , conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="3011b-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="3011b-110">Forneça um [DirectoryInfo](/dotnet/api/system.io.directoryinfo) apontando para o repositório onde as chaves devem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="3011b-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="3011b-111">O `DirectoryInfo` pode apontar para um diretório no computador local ou pode apontar para uma pasta em um compartilhamento de rede.</span><span class="sxs-lookup"><span data-stu-id="3011b-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="3011b-112">Se estiver apontando para um diretório no computador local (e o cenário é que somente os aplicativos no computador local exigem acesso para usar esse repositório), considere usar o [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (no Windows) para criptografar as chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="3011b-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="3011b-113">Caso contrário, considere o uso de um [certificado X. 509](xref:security/data-protection/implementation/key-encryption-at-rest) para criptografar chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="3011b-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="3011b-114">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="3011b-114">Azure Storage</span></span>

<span data-ttu-id="3011b-115">O pacote [Microsoft. AspNetCore. dataprotection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) permite armazenar chaves de proteção de dados no armazenamento de BLOBs do Azure.</span><span class="sxs-lookup"><span data-stu-id="3011b-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="3011b-116">As chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="3011b-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="3011b-117">Os aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.</span><span class="sxs-lookup"><span data-stu-id="3011b-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="3011b-118">Para configurar o provedor de armazenamento de BLOBs do Azure, chame uma das sobrecargas de [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) .</span><span class="sxs-lookup"><span data-stu-id="3011b-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="3011b-119">Se o aplicativo Web estiver sendo executado como um serviço do Azure, os tokens de autenticação poderão ser criados automaticamente usando [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="3011b-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

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

<span data-ttu-id="3011b-120">Veja [mais detalhes sobre como configurar a autenticação de serviço a serviço.](/azure/key-vault/service-to-service-authentication)</span><span class="sxs-lookup"><span data-stu-id="3011b-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="3011b-121">Redis</span><span class="sxs-lookup"><span data-stu-id="3011b-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3011b-122">O pacote [Microsoft. AspNetCore. dataprotection. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) permite armazenar chaves de proteção de dados em um cache Redis.</span><span class="sxs-lookup"><span data-stu-id="3011b-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="3011b-123">As chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="3011b-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="3011b-124">Os aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.</span><span class="sxs-lookup"><span data-stu-id="3011b-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3011b-125">O pacote [Microsoft. AspNetCore. dataprotection. Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) permite armazenar chaves de proteção de dados em um cache Redis.</span><span class="sxs-lookup"><span data-stu-id="3011b-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="3011b-126">As chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="3011b-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="3011b-127">Os aplicativos podem compartilhar cookies de autenticação ou proteção CSRF em vários servidores.</span><span class="sxs-lookup"><span data-stu-id="3011b-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3011b-128">Para configurar o no Redis, chame uma das sobrecargas de [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) :</span><span class="sxs-lookup"><span data-stu-id="3011b-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="3011b-129">Para configurar o no Redis, chame uma das sobrecargas de [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) :</span><span class="sxs-lookup"><span data-stu-id="3011b-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="3011b-130">Para mais informações, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="3011b-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="3011b-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="3011b-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="3011b-132">Cache Redis do Azure</span><span class="sxs-lookup"><span data-stu-id="3011b-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="3011b-133">exemplos de ASPNET/dataprotection</span><span class="sxs-lookup"><span data-stu-id="3011b-133">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="3011b-134">Registro</span><span class="sxs-lookup"><span data-stu-id="3011b-134">Registry</span></span>

<span data-ttu-id="3011b-135">**Aplica-se somente a implantações do Windows.**</span><span class="sxs-lookup"><span data-stu-id="3011b-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="3011b-136">Às vezes, o aplicativo pode não ter acesso de gravação ao sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="3011b-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="3011b-137">Considere um cenário em que um aplicativo está sendo executado como uma conta de serviço virtual (como a identidade do pool de aplicativos *w3wp. exe*).</span><span class="sxs-lookup"><span data-stu-id="3011b-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="3011b-138">Nesses casos, o administrador pode provisionar uma chave do registro acessível pela identidade da conta de serviço.</span><span class="sxs-lookup"><span data-stu-id="3011b-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="3011b-139">Chame o método de extensão [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) , conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="3011b-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="3011b-140">Forneça uma [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) apontando para o local onde as chaves de criptografia devem ser armazenadas:</span><span class="sxs-lookup"><span data-stu-id="3011b-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="3011b-141">É recomendável usar o [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) para criptografar as chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="3011b-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="3011b-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="3011b-142">Entity Framework Core</span></span>

<span data-ttu-id="3011b-143">O pacote [Microsoft. AspNetCore. dataprotection. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) fornece um mecanismo para armazenar as chaves de proteção de dados em um banco usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3011b-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="3011b-144">O pacote NuGet `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` deve ser adicionado ao arquivo de projeto, ele não faz parte do [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3011b-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3011b-145">Com esse pacote, as chaves podem ser compartilhadas entre várias instâncias de um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="3011b-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="3011b-146">Para configurar o provedor de EF Core, chame o método [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) :</span><span class="sxs-lookup"><span data-stu-id="3011b-146">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="3011b-147">O parâmetro genérico, `TContext`, deve herdar de [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e implementar [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="3011b-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="3011b-148">Crie a tabela `DataProtectionKeys`.</span><span class="sxs-lookup"><span data-stu-id="3011b-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3011b-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3011b-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3011b-150">Execute os seguintes comandos na janela do **console do Gerenciador de pacotes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="3011b-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3011b-151">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="3011b-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3011b-152">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3011b-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="3011b-153">`MyKeysContext` é a `DbContext` definida no exemplo de código anterior.</span><span class="sxs-lookup"><span data-stu-id="3011b-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="3011b-154">Se você estiver usando um `DbContext` com um nome diferente, substitua o nome do `DbContext` por `MyKeysContext`.</span><span class="sxs-lookup"><span data-stu-id="3011b-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="3011b-155">A classe/entidade `DataProtectionKeys` adota a estrutura mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="3011b-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="3011b-156">Propriedade/campo</span><span class="sxs-lookup"><span data-stu-id="3011b-156">Property/Field</span></span> | <span data-ttu-id="3011b-157">Tipo CLR</span><span class="sxs-lookup"><span data-stu-id="3011b-157">CLR Type</span></span> | <span data-ttu-id="3011b-158">Tipo SQL</span><span class="sxs-lookup"><span data-stu-id="3011b-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="3011b-159">`int`, CP, não nulo</span><span class="sxs-lookup"><span data-stu-id="3011b-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="3011b-160">`nvarchar(MAX)`, nulo</span><span class="sxs-lookup"><span data-stu-id="3011b-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="3011b-161">`nvarchar(MAX)`, nulo</span><span class="sxs-lookup"><span data-stu-id="3011b-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="3011b-162">Repositório de chaves personalizado</span><span class="sxs-lookup"><span data-stu-id="3011b-162">Custom key repository</span></span>

<span data-ttu-id="3011b-163">Se os mecanismos na caixa não forem apropriados, o desenvolvedor poderá especificar seu próprio mecanismo de persistência de chave fornecendo um [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)personalizado.</span><span class="sxs-lookup"><span data-stu-id="3011b-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
