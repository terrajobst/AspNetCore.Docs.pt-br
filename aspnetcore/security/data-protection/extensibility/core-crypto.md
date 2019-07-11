---
title: Extensibilidade da criptografia básica no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer e a fábrica de nível superior.
ms.author: riande
ms.date: 08/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: a5f651e3313cc579b995b45905826a5bffcc241c
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814699"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="e8bab-103">Extensibilidade da criptografia básica no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e8bab-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="e8bab-104">Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.</span><span class="sxs-lookup"><span data-stu-id="e8bab-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="e8bab-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="e8bab-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="e8bab-106">O **IAuthenticatedEncryptor** interface é o bloco de construção básico do subsistema de criptografia.</span><span class="sxs-lookup"><span data-stu-id="e8bab-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="e8bab-107">Geralmente é um IAuthenticatedEncryptor por chave e a instância de IAuthenticatedEncryptor encapsula todos os materiais de chave de criptografia e algorítmicos informações necessárias para executar operações criptográficas.</span><span class="sxs-lookup"><span data-stu-id="e8bab-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="e8bab-108">Como o nome sugere, o tipo é responsável por fornecer serviços de criptografia e descriptografia autenticados.</span><span class="sxs-lookup"><span data-stu-id="e8bab-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="e8bab-109">Ele expõe as APIs a seguir.</span><span class="sxs-lookup"><span data-stu-id="e8bab-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="e8bab-110">O método Encrypt retorna um blob que inclui o texto não criptografado adulterem e uma marca de autenticação.</span><span class="sxs-lookup"><span data-stu-id="e8bab-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="e8bab-111">A marca de autenticação deve abranger os dados adicionais autenticados (AAD), embora o AAD em si não precisa ser recuperado por meio a carga final.</span><span class="sxs-lookup"><span data-stu-id="e8bab-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="e8bab-112">O método Decrypt valida a marca de autenticação e retorna a carga deciphered.</span><span class="sxs-lookup"><span data-stu-id="e8bab-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="e8bab-113">Todas as falhas (exceto ArgumentNullException e similares) devem ser homogenized para CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="e8bab-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="e8bab-114">A própria instância IAuthenticatedEncryptor, na verdade, não precisa conter o material da chave.</span><span class="sxs-lookup"><span data-stu-id="e8bab-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="e8bab-115">Por exemplo, a implementação pudesse ser delegado para um HSM para todas as operações.</span><span class="sxs-lookup"><span data-stu-id="e8bab-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="e8bab-116">Como criar um IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="e8bab-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8bab-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8bab-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e8bab-118">O **IAuthenticatedEncryptorFactory** interface representa um tipo que sabe como criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância.</span><span class="sxs-lookup"><span data-stu-id="e8bab-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="e8bab-119">Sua API é da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="e8bab-119">Its API is as follows.</span></span>

* <span data-ttu-id="e8bab-120">CreateEncryptorInstance (chave IKey): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="e8bab-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="e8bab-121">Para qualquer determinada instância de IKey, os qualquer criptografadores autenticados criados por seu método CreateEncryptorInstance devem ser considerados equivalentes, como no exemplo de código a seguir.</span><span class="sxs-lookup"><span data-stu-id="e8bab-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8bab-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8bab-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e8bab-123">O **IAuthenticatedEncryptorDescriptor** interface representa um tipo que sabe como criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância.</span><span class="sxs-lookup"><span data-stu-id="e8bab-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="e8bab-124">Sua API é da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="e8bab-124">Its API is as follows.</span></span>

* <span data-ttu-id="e8bab-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="e8bab-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="e8bab-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="e8bab-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="e8bab-127">Como IAuthenticatedEncryptor, uma instância de IAuthenticatedEncryptorDescriptor será assumida para encapsular uma chave específica.</span><span class="sxs-lookup"><span data-stu-id="e8bab-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="e8bab-128">Isso significa que para determinada instância IAuthenticatedEncryptorDescriptor, qualquer criptografadores autenticados criados por seu método CreateEncryptorInstance devem ser considerados equivalentes, como mostra o exemplo de código a seguir.</span><span class="sxs-lookup"><span data-stu-id="e8bab-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="e8bab-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2. x somente)</span><span class="sxs-lookup"><span data-stu-id="e8bab-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8bab-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8bab-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e8bab-131">O **IAuthenticatedEncryptorDescriptor** interface representa um tipo que sabe como exportar em si para XML.</span><span class="sxs-lookup"><span data-stu-id="e8bab-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="e8bab-132">Sua API é da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="e8bab-132">Its API is as follows.</span></span>

* <span data-ttu-id="e8bab-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="e8bab-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8bab-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8bab-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="e8bab-135">Serialização XML</span><span class="sxs-lookup"><span data-stu-id="e8bab-135">XML Serialization</span></span>

<span data-ttu-id="e8bab-136">A principal diferença entre IAuthenticatedEncryptor e IAuthenticatedEncryptorDescriptor é que o descritor sabe como criar o Criptografador e fornecê-lo com os argumentos válidos.</span><span class="sxs-lookup"><span data-stu-id="e8bab-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="e8bab-137">Considere um IAuthenticatedEncryptor cuja implementação se baseia em SymmetricAlgorithm e KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="e8bab-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="e8bab-138">Trabalho do Criptografador é consumir esses tipos, mas não necessariamente sabe onde esses tipos de origem, para que ele realmente não é possível gravar uma descrição apropriada sobre como recriar em si se o aplicativo for reiniciado.</span><span class="sxs-lookup"><span data-stu-id="e8bab-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="e8bab-139">O descritor atua como um nível mais alto sobre isso.</span><span class="sxs-lookup"><span data-stu-id="e8bab-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="e8bab-140">Uma vez que o descritor sabe como criar a instância do Criptografador (por exemplo, ele sabe como criar os algoritmos necessários), ele pode serializar esse conhecimento no formato XML, de modo que a instância do Criptografador pode ser recriada após a redefinição de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e8bab-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="e8bab-141">O descritor de pode ser serializado por meio de sua rotina de ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="e8bab-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="e8bab-142">Essa rotina retorna um XmlSerializedDescriptorInfo que contém duas propriedades: a representação de XElement do descritor e o tipo que representa um [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) que pode ser usado para ressuscitar Esse descritor de dada a XElement correspondente.</span><span class="sxs-lookup"><span data-stu-id="e8bab-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="e8bab-143">O descritor serializado pode conter informações confidenciais, como material de chave de criptografia.</span><span class="sxs-lookup"><span data-stu-id="e8bab-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="e8bab-144">O sistema de proteção de dados tem suporte interno para criptografar informações antes de ele ser mantido no armazenamento.</span><span class="sxs-lookup"><span data-stu-id="e8bab-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="e8bab-145">Para tirar proveito disso, o descritor deve marcar o elemento que contém informações confidenciais com o nome do atributo "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), valor "true".</span><span class="sxs-lookup"><span data-stu-id="e8bab-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="e8bab-146">Há um auxiliar de API para definir esse atributo.</span><span class="sxs-lookup"><span data-stu-id="e8bab-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="e8bab-147">Chame o método de extensão que XElement.markasrequiresencryption() localizada no namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="e8bab-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="e8bab-148">Também pode haver casos em que o descritor serializado não contém informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="e8bab-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="e8bab-149">Considere novamente o caso de uma chave de criptografia armazenada em um HSM.</span><span class="sxs-lookup"><span data-stu-id="e8bab-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="e8bab-150">O descritor não é possível gravar o material da chave ao serializar em si, pois o HSM não expõe o material em forma de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="e8bab-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="e8bab-151">Em vez disso, o descritor pode escrever-out da versão encapsulado de chave da chave (se o HSM permite exportar dessa forma) ou o identificador exclusivo do HSM a chave.</span><span class="sxs-lookup"><span data-stu-id="e8bab-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="e8bab-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="e8bab-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="e8bab-153">O **IAuthenticatedEncryptorDescriptorDeserializer** interface representa um tipo que sabe como desserializar uma instância de IAuthenticatedEncryptorDescriptor de um XElement.</span><span class="sxs-lookup"><span data-stu-id="e8bab-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="e8bab-154">Ela expõe um único método:</span><span class="sxs-lookup"><span data-stu-id="e8bab-154">It exposes a single method:</span></span>

* <span data-ttu-id="e8bab-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="e8bab-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="e8bab-156">O método ImportFromXml leva o XElement que foi retornado por [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) e cria o equivalente a IAuthenticatedEncryptorDescriptor original.</span><span class="sxs-lookup"><span data-stu-id="e8bab-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="e8bab-157">Tipos que implementam IAuthenticatedEncryptorDescriptorDeserializer devem ter um dos dois construtores públicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="e8bab-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="e8bab-158">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="e8bab-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="e8bab-159">.ctor()</span><span class="sxs-lookup"><span data-stu-id="e8bab-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="e8bab-160">O IServiceProvider passado para o construtor pode ser nulo.</span><span class="sxs-lookup"><span data-stu-id="e8bab-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="e8bab-161">A fábrica de nível superior</span><span class="sxs-lookup"><span data-stu-id="e8bab-161">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="e8bab-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="e8bab-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="e8bab-163">O **AlgorithmConfiguration** classe representa um tipo que sabe como criar [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8bab-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="e8bab-164">Ele expõe uma única API.</span><span class="sxs-lookup"><span data-stu-id="e8bab-164">It exposes a single API.</span></span>

* <span data-ttu-id="e8bab-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="e8bab-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="e8bab-166">Pense AlgorithmConfiguration como a fábrica de nível superior.</span><span class="sxs-lookup"><span data-stu-id="e8bab-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="e8bab-167">A configuração serve como um modelo.</span><span class="sxs-lookup"><span data-stu-id="e8bab-167">The configuration serves as a template.</span></span> <span data-ttu-id="e8bab-168">Ele encapsula informações algorítmicos (por exemplo, essa configuração produz descritores com uma chave mestra de AES-128-GCM), mas ele ainda não tiver associado uma chave específica.</span><span class="sxs-lookup"><span data-stu-id="e8bab-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="e8bab-169">Quando CreateNewDescriptor é chamado, material de chave novo é criado exclusivamente para essa chamada e um novo IAuthenticatedEncryptorDescriptor é produzido, que encapsula esse material da chave e as informações de algorítmicos necessária para consumir o material.</span><span class="sxs-lookup"><span data-stu-id="e8bab-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="e8bab-170">O material da chave pode ser criado no software (e mantido na memória), poderia ser criado e mantido em um HSM e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="e8bab-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="e8bab-171">O ponto fundamental é que as duas chamadas para CreateNewDescriptor nunca devem criar instâncias de IAuthenticatedEncryptorDescriptor equivalentes.</span><span class="sxs-lookup"><span data-stu-id="e8bab-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="e8bab-172">O tipo de AlgorithmConfiguration serve como ponto de entrada para as rotinas de criação da chave como [chave automática sem interrupção](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="e8bab-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="e8bab-173">Para alterar a implementação para todas as chaves de futuros, defina a propriedade de AuthenticatedEncryptorConfiguration em KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="e8bab-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="e8bab-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="e8bab-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="e8bab-175">O **IAuthenticatedEncryptorConfiguration** interface representa um tipo que sabe como criar [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instâncias.</span><span class="sxs-lookup"><span data-stu-id="e8bab-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="e8bab-176">Ele expõe uma única API.</span><span class="sxs-lookup"><span data-stu-id="e8bab-176">It exposes a single API.</span></span>

* <span data-ttu-id="e8bab-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="e8bab-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="e8bab-178">Pense IAuthenticatedEncryptorConfiguration como a fábrica de nível superior.</span><span class="sxs-lookup"><span data-stu-id="e8bab-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="e8bab-179">A configuração serve como um modelo.</span><span class="sxs-lookup"><span data-stu-id="e8bab-179">The configuration serves as a template.</span></span> <span data-ttu-id="e8bab-180">Ele encapsula informações algorítmicos (por exemplo, essa configuração produz descritores com uma chave mestra de AES-128-GCM), mas ele ainda não tiver associado uma chave específica.</span><span class="sxs-lookup"><span data-stu-id="e8bab-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="e8bab-181">Quando CreateNewDescriptor é chamado, material de chave novo é criado exclusivamente para essa chamada e um novo IAuthenticatedEncryptorDescriptor é produzido, que encapsula esse material da chave e as informações de algorítmicos necessária para consumir o material.</span><span class="sxs-lookup"><span data-stu-id="e8bab-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="e8bab-182">O material da chave pode ser criado no software (e mantido na memória), poderia ser criado e mantido em um HSM e assim por diante.</span><span class="sxs-lookup"><span data-stu-id="e8bab-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="e8bab-183">O ponto fundamental é que as duas chamadas para CreateNewDescriptor nunca devem criar instâncias de IAuthenticatedEncryptorDescriptor equivalentes.</span><span class="sxs-lookup"><span data-stu-id="e8bab-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="e8bab-184">O tipo de IAuthenticatedEncryptorConfiguration serve como ponto de entrada para as rotinas de criação da chave como [chave automática sem interrupção](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="e8bab-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="e8bab-185">Para alterar a implementação para todas as chaves de futuros, registre um singleton IAuthenticatedEncryptorConfiguration no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="e8bab-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
