---
title: Extensibilidade de gerenciamento de chaves no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a extensibilidade do gerenciamento de chaves de proteção de dados do ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284598"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="00780-103">Extensibilidade de gerenciamento de chaves no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="00780-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="00780-104">Leia as [gerenciamento de chaves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) seção antes de ler esta seção, pois ele explica alguns dos conceitos fundamentais dessas APIs.</span><span class="sxs-lookup"><span data-stu-id="00780-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="00780-105">Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.</span><span class="sxs-lookup"><span data-stu-id="00780-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="00780-106">Chave</span><span class="sxs-lookup"><span data-stu-id="00780-106">Key</span></span>

<span data-ttu-id="00780-107">O `IKey` interface é a representação básica de uma chave no sistema de criptografia.</span><span class="sxs-lookup"><span data-stu-id="00780-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="00780-108">A chave do termo é usada aqui no sentido abstrato, não no sentido literal "criptográfico do material da chave".</span><span class="sxs-lookup"><span data-stu-id="00780-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="00780-109">Uma chave tem as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="00780-109">A key has the following properties:</span></span>

* <span data-ttu-id="00780-110">Datas de expiração, criação e ativação</span><span class="sxs-lookup"><span data-stu-id="00780-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="00780-111">Status de revogação</span><span class="sxs-lookup"><span data-stu-id="00780-111">Revocation status</span></span>

* <span data-ttu-id="00780-112">Identificador de chave (um GUID)</span><span class="sxs-lookup"><span data-stu-id="00780-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="00780-113">Além disso, `IKey` expõe uma `CreateEncryptor` método que pode ser usado para criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância vinculado a essa chave.</span><span class="sxs-lookup"><span data-stu-id="00780-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="00780-114">Além disso, `IKey` expõe uma `CreateEncryptorInstance` método que pode ser usado para criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância vinculado a essa chave.</span><span class="sxs-lookup"><span data-stu-id="00780-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="00780-115">Há uma API para recuperar o material criptográfico bruto de um `IKey` instância.</span><span class="sxs-lookup"><span data-stu-id="00780-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="00780-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="00780-116">IKeyManager</span></span>

<span data-ttu-id="00780-117">O `IKeyManager` interface representa um objeto responsável pelo armazenamento de chaves geral, a recuperação e a manipulação.</span><span class="sxs-lookup"><span data-stu-id="00780-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="00780-118">Ele expõe três operações de alto nível:</span><span class="sxs-lookup"><span data-stu-id="00780-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="00780-119">Criar uma nova chave e mantê-lo no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="00780-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="00780-120">Obter todas as chaves de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="00780-120">Get all keys from storage.</span></span>

* <span data-ttu-id="00780-121">Revogar uma ou mais chaves e persistir as informações de revogação para o armazenamento.</span><span class="sxs-lookup"><span data-stu-id="00780-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="00780-122">Escrevendo um `IKeyManager` é uma tarefa muito avançada e a maioria dos desenvolvedores não deve tentá-lo.</span><span class="sxs-lookup"><span data-stu-id="00780-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="00780-123">Em vez disso, a maioria dos desenvolvedores deve tirar vantagem dos recursos oferecidos pelo [XmlKeyManager](#xmlkeymanager) classe.</span><span class="sxs-lookup"><span data-stu-id="00780-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="00780-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="00780-124">XmlKeyManager</span></span>

<span data-ttu-id="00780-125">O `XmlKeyManager` tipo é a implementação concreta da caixa de entrada da `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="00780-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="00780-126">Ele fornece vários recursos úteis, incluindo caução de chaves e criptografia de chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="00780-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="00780-127">Chaves nesse sistema são representadas como elementos XML (especificamente, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="00780-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="00780-128">`XmlKeyManager` depende de vários outros componentes no decorrer de cumprir suas tarefas:</span><span class="sxs-lookup"><span data-stu-id="00780-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="00780-129">`AlgorithmConfiguration`, que determina os algoritmos usados por novas chaves.</span><span class="sxs-lookup"><span data-stu-id="00780-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="00780-130">`IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="00780-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="00780-131">`IXmlEncryptor` [opcional], que permite criptografar as chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="00780-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="00780-132">`IKeyEscrowSink` [opcional], que fornece serviços de caução de chaves.</span><span class="sxs-lookup"><span data-stu-id="00780-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="00780-133">`IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="00780-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="00780-134">`IXmlEncryptor` [opcional], que permite criptografar as chaves em repouso.</span><span class="sxs-lookup"><span data-stu-id="00780-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="00780-135">`IKeyEscrowSink` [opcional], que fornece serviços de caução de chaves.</span><span class="sxs-lookup"><span data-stu-id="00780-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="00780-136">Abaixo estão os diagramas de alto nível que indicam como esses componentes são vinculados dentro `XmlKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="00780-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Criação da chave](key-management/_static/keycreation2.png)

<span data-ttu-id="00780-138">*Criação de chave / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="00780-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="00780-139">Na implementação de `CreateNewKey`, o `AlgorithmConfiguration` componente é usado para criar um único `IAuthenticatedEncryptorDescriptor`, que é então serializado como XML.</span><span class="sxs-lookup"><span data-stu-id="00780-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="00780-140">Se um coletor de caução de chave estiver presente, o XML bruto de (não criptografado) é fornecido para o coletor para armazenamento de longo prazo.</span><span class="sxs-lookup"><span data-stu-id="00780-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="00780-141">O XML não criptografado é executado por meio de um `IXmlEncryptor` (se necessário) para gerar o documento XML criptografado.</span><span class="sxs-lookup"><span data-stu-id="00780-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="00780-142">Este documento criptografado é persistido no armazenamento de longo prazo via o `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="00780-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="00780-143">(Se nenhum `IXmlEncryptor` é configurado, o documento não criptografado é persistido no `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="00780-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recuperação de chave](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Criação da chave](key-management/_static/keycreation1.png)

<span data-ttu-id="00780-146">*Criação de chave / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="00780-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="00780-147">Na implementação de `CreateNewKey`, o `IAuthenticatedEncryptorConfiguration` componente é usado para criar um único `IAuthenticatedEncryptorDescriptor`, que é então serializado como XML.</span><span class="sxs-lookup"><span data-stu-id="00780-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="00780-148">Se um coletor de caução de chave estiver presente, o XML bruto de (não criptografado) é fornecido para o coletor para armazenamento de longo prazo.</span><span class="sxs-lookup"><span data-stu-id="00780-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="00780-149">O XML não criptografado é executado por meio de um `IXmlEncryptor` (se necessário) para gerar o documento XML criptografado.</span><span class="sxs-lookup"><span data-stu-id="00780-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="00780-150">Este documento criptografado é persistido no armazenamento de longo prazo via o `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="00780-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="00780-151">(Se nenhum `IXmlEncryptor` é configurado, o documento não criptografado é persistido no `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="00780-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Recuperação de chave](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="00780-153">*Recuperação de chave / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="00780-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="00780-154">Na implementação de `GetAllKeys`, o XML documenta as chaves que representa e revogações são lidas do subjacente `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="00780-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="00780-155">Se esses documentos são criptografados, o sistema automaticamente descriptografá-los.</span><span class="sxs-lookup"><span data-stu-id="00780-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="00780-156">`XmlKeyManager` cria o apropriada `IAuthenticatedEncryptorDescriptorDeserializer` instâncias para desserializar os documentos de volta para o `IAuthenticatedEncryptorDescriptor` instâncias, que, em seguida, são encapsuladas em indivíduo `IKey` instâncias.</span><span class="sxs-lookup"><span data-stu-id="00780-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="00780-157">Esta coleção de `IKey` instâncias é retornado ao chamador.</span><span class="sxs-lookup"><span data-stu-id="00780-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="00780-158">Obter mais informações sobre os elementos XML específicos podem ser encontradas na [documentos de formato de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="00780-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="00780-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-159">IXmlRepository</span></span>

<span data-ttu-id="00780-160">O `IXmlRepository` interface representa um tipo que pode persistir o XML para e recuperar o XML de um armazenamento de backup.</span><span class="sxs-lookup"><span data-stu-id="00780-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="00780-161">Ele expõe duas APIs:</span><span class="sxs-lookup"><span data-stu-id="00780-161">It exposes two APIs:</span></span>

* <span data-ttu-id="00780-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="00780-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="00780-163">Implementações de `IXmlRepository` não precisam analisar o XML passar através deles.</span><span class="sxs-lookup"><span data-stu-id="00780-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="00780-164">Eles devem tratar os documentos XML como opaco e permitir que camadas superiores se preocupar sobre como gerar e analisar os documentos.</span><span class="sxs-lookup"><span data-stu-id="00780-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="00780-165">Há quatro tipos concretos internos que implementam `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="00780-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="00780-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="00780-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="00780-168">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="00780-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="00780-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="00780-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="00780-172">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="00780-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="00780-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="00780-174">Consulte a [documentos de provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="00780-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="00780-175">Registrando um personalizado `IXmlRepository` é apropriado ao usar um armazenamento de backup diferente (por exemplo, armazenamento de tabela do Azure).</span><span class="sxs-lookup"><span data-stu-id="00780-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="00780-176">Para alterar o repositório padrão todo o aplicativo, registre um personalizado `IXmlRepository` instância:</span><span class="sxs-lookup"><span data-stu-id="00780-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="00780-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="00780-177">IXmlEncryptor</span></span>

<span data-ttu-id="00780-178">O `IXmlEncryptor` interface representa um tipo que pode criptografar um elemento XML de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="00780-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="00780-179">Ela apresenta uma única API:</span><span class="sxs-lookup"><span data-stu-id="00780-179">It exposes a single API:</span></span>

* <span data-ttu-id="00780-180">Encrypt(XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="00780-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="00780-181">Se um serializado `IAuthenticatedEncryptorDescriptor` contém quaisquer elementos marcados como "requer criptografia", em seguida, `XmlKeyManager` executará esses elementos por meio do configurado `IXmlEncryptor`do `Encrypt` método e ele serão mantido o elemento adulterem em vez de elemento de texto sem formatação para o `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="00780-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="00780-182">A saída a `Encrypt` método é um `EncryptedXmlInfo` objeto.</span><span class="sxs-lookup"><span data-stu-id="00780-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="00780-183">Esse objeto é um wrapper que contém os dois o resultante adulterem `XElement` e o tipo que representa um `IXmlDecryptor` que pode ser usado para decifrar o elemento correspondente.</span><span class="sxs-lookup"><span data-stu-id="00780-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="00780-184">Há quatro tipos concretos internos que implementam `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="00780-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="00780-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="00780-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="00780-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="00780-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="00780-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="00780-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="00780-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="00780-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="00780-189">Consulte a [criptografia de chave em documentos do rest](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="00780-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="00780-190">Para alterar o mecanismo padrão de chave criptografia em repouso todo o aplicativo, registre um personalizado `IXmlEncryptor` instância:</span><span class="sxs-lookup"><span data-stu-id="00780-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="00780-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="00780-191">IXmlDecryptor</span></span>

<span data-ttu-id="00780-192">O `IXmlDecryptor` interface representa um tipo que sabe como descriptografar um `XElement` que foi adulterem por meio de um `IXmlEncryptor`.</span><span class="sxs-lookup"><span data-stu-id="00780-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="00780-193">Ela apresenta uma única API:</span><span class="sxs-lookup"><span data-stu-id="00780-193">It exposes a single API:</span></span>

* <span data-ttu-id="00780-194">Descriptografe (encryptedElement XElement): XElement</span><span class="sxs-lookup"><span data-stu-id="00780-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="00780-195">O `Decrypt` método desfaz a criptografia executada pelo `IXmlEncryptor.Encrypt`.</span><span class="sxs-lookup"><span data-stu-id="00780-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="00780-196">Em geral, cada concreto `IXmlEncryptor` implementação terá um concreto correspondente `IXmlDecryptor` implementação.</span><span class="sxs-lookup"><span data-stu-id="00780-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="00780-197">Tipos que implementam `IXmlDecryptor` deve ter um dos dois construtores públicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="00780-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="00780-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="00780-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="00780-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="00780-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="00780-200">O `IServiceProvider` passado para o construtor pode ser nulo.</span><span class="sxs-lookup"><span data-stu-id="00780-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="00780-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="00780-201">IKeyEscrowSink</span></span>

<span data-ttu-id="00780-202">O `IKeyEscrowSink` interface representa um tipo que pode executar a caução de informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="00780-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="00780-203">Lembre-se de que descritores serializados podem conter informações confidenciais (por exemplo, o material criptográfico), e esse é o que levou à introdução do [IXmlEncryptor](#ixmlencryptor) digite em primeiro lugar.</span><span class="sxs-lookup"><span data-stu-id="00780-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="00780-204">No entanto, acidentes acontecem e anéis de chave podem ser excluídos ou corrompidos.</span><span class="sxs-lookup"><span data-stu-id="00780-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="00780-205">A interface de caução fornece uma hachura de escape de emergência, permitindo o acesso ao XML bruto serializado antes que ele é transformado por qualquer configurados [IXmlEncryptor](#ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="00780-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="00780-206">A interface expõe uma única API:</span><span class="sxs-lookup"><span data-stu-id="00780-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="00780-207">Store (keyId Guid, o elemento de XElement)</span><span class="sxs-lookup"><span data-stu-id="00780-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="00780-208">Cabe ao `IKeyEscrowSink` implementação para lidar com o elemento fornecido de maneira segura consistente com a política de negócios.</span><span class="sxs-lookup"><span data-stu-id="00780-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="00780-209">Uma possível implementação pode ser para o coletor de caução criptografar o elemento XML usando um certificado x. 509 corporativo reconhecido onde chave privada do certificado tenha sido mantida em garantia; o `CertificateXmlEncryptor` tipo pode ajudar com isso.</span><span class="sxs-lookup"><span data-stu-id="00780-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="00780-210">O `IKeyEscrowSink` implementação também é responsável por manter o elemento fornecido adequadamente.</span><span class="sxs-lookup"><span data-stu-id="00780-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="00780-211">Por padrão nenhum mecanismo de caução está habilitado, embora os administradores de servidor podem [configurar isso globalmente](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="00780-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="00780-212">Ele também pode ser configurado por meio de programação por meio de `IDataProtectionBuilder.AddKeyEscrowSink` método conforme mostrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="00780-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="00780-213">O `AddKeyEscrowSink` espelho de sobrecargas do método de `IServiceCollection.AddSingleton` e `IServiceCollection.AddInstance` sobrecargas, como `IKeyEscrowSink` instâncias devem ser singletons.</span><span class="sxs-lookup"><span data-stu-id="00780-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="00780-214">Se vários `IKeyEscrowSink` instâncias registradas, cada um deles será chamado durante a geração de chave, para que as chaves podem ser mantida em garantia para diversos mecanismos simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="00780-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="00780-215">Há uma API para ler o material de um `IKeyEscrowSink` instância.</span><span class="sxs-lookup"><span data-stu-id="00780-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="00780-216">Isso é consistente com a teoria de design do mecanismo de caução: ele deve disponibilizar o material da chave para uma autoridade confiável, e uma vez que o aplicativo em si não é uma autoridade confiável, ele não deve ter acesso ao seu próprio caucionada material.</span><span class="sxs-lookup"><span data-stu-id="00780-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="00780-217">O código de exemplo a seguir demonstra como criar e registrar um `IKeyEscrowSink` onde as chaves são mantida em garantia, de modo que somente os membros do grupo "administradores de CONTOSODomain" poderá recuperá-los.</span><span class="sxs-lookup"><span data-stu-id="00780-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="00780-218">Para executar este exemplo, você deve estar em um domínio do Windows 8 / máquina Windows Server 2012 e o controlador de domínio devem ser Windows Server 2012 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="00780-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
