---
title: Extensibilidade de criptografia de núcleo no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer e a fábrica de nível superior.
ms.author: riande
ms.date: 08/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: a5f651e3313cc579b995b45905826a5bffcc241c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663565"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Extensibilidade de criptografia de núcleo no ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

A interface **IAuthenticatedEncryptor** é o bloco de construção básico do subsistema de criptografia. Geralmente, há um IAuthenticatedEncryptor por chave, e a instância IAuthenticatedEncryptor encapsula todo o material de chave de criptografia e as informações de algoritmos necessárias para executar operações criptográficas.

Como o nome sugere, o tipo é responsável por fornecer criptografia autenticada e serviços de descriptografia. Ele expõe as duas APIs a seguir.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

O método Encrypt retorna um blob que inclui o texto não criptografado codificado e uma marca de autenticação. A marca de autenticação deve abranger os dados autenticados adicionais (AAD), embora o próprio AAD não precise ser recuperável a partir da carga final. O método Decrypt valida a marca de autenticação e retorna a carga decifrada. Todas as falhas (exceto ArgumentNullException e similar) devem ser homogenized para CryptographicException.

> [!NOTE]
> A instância de IAuthenticatedEncryptor em si não precisa realmente conter o material da chave. Por exemplo, a implementação pode delegar a um HSM para todas as operações.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Como criar um IAuthenticatedEncryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A interface **IAuthenticatedEncryptorFactory** representa um tipo que sabe como criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Sua API é a seguinte:

* CreateEncryptorInstance (chave de IKey): IAuthenticatedEncryptor

Para qualquer instância IKey específica, todos os criptografadores autenticados criados por seu método CreateEncryptorInstance devem ser considerados equivalentes, como no exemplo de código abaixo.

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

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

A interface **IAuthenticatedEncryptorDescriptor** representa um tipo que sabe como criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Sua API é a seguinte:

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Como IAuthenticatedEncryptor, uma instância de IAuthenticatedEncryptorDescriptor é considerada para encapsular uma chave específica. Isso significa que para qualquer instância IAuthenticatedEncryptorDescriptor específica, todos os criptografadores autenticados criados por seu método CreateEncryptorInstance devem ser considerados equivalentes, como no exemplo de código abaixo.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (somente ASP.NET Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A interface **IAuthenticatedEncryptorDescriptor** representa um tipo que sabe como exportar a si mesmo para XML. Sua API é a seguinte:

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Serialização XML

A principal diferença entre IAuthenticatedEncryptor e IAuthenticatedEncryptorDescriptor é que o descritor sabe como criar o criptografador e fornecê-lo com argumentos válidos. Considere um IAuthenticatedEncryptor cuja implementação se baseia em SymmetricAlgorithm e KeyedHashAlgorithm. O trabalho do criptografador é consumir esses tipos, mas não necessariamente sabe de onde esses tipos vieram, portanto, não pode realmente escrever uma descrição adequada de como recriar a si mesmo se o aplicativo for reiniciado. O descritor atua como um nível superior sobre isso. Como o descritor sabe como criar a instância do criptografador (por exemplo, ele sabe como criar os algoritmos necessários), ele pode serializar esse conhecimento em formato XML para que a instância do criptografador possa ser recriada após a redefinição do aplicativo.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

O descritor pode ser serializado por meio de sua rotina ExportToXml. Essa rotina retorna um XmlSerializedDescriptorInfo que contém duas propriedades: a representação XElement do descritor e o tipo que representa um [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) que pode ser usado para ressuscitar esse descritor, considerando o XElement correspondente.

O descritor serializado pode conter informações confidenciais, como o material de chave de criptografia. O sistema de proteção de dados tem suporte interno para criptografar informações antes que ela seja persistida no armazenamento. Para tirar proveito disso, o descritor deve marcar o elemento que contém informações confidenciais com o nome do atributo "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), valor "true".

>[!TIP]
> Há uma API auxiliar para definir esse atributo. Chame o método de extensão XElement. MarkAsRequiresEncryption () localizado no namespace Microsoft. AspNetCore. dataprotection. AuthenticatedEncryption. ConfigurationModel.

Também pode haver casos em que o descritor serializado não contém informações confidenciais. Considere novamente o caso de uma chave de criptografia armazenada em um HSM. O descritor não pode gravar o material da chave ao se serializar, pois o HSM não exporá o material em formato de texto sem formatação. Em vez disso, o descritor pode gravar a versão com encapsulamento de chave da chave (se o HSM permitir exportar dessa maneira) ou o próprio identificador exclusivo do HSM para a chave.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

A interface **IAuthenticatedEncryptorDescriptorDeserializer** representa um tipo que sabe como desserializar uma instância IAuthenticatedEncryptorDescriptor de um XElement. Ele expõe um único método:

* ImportFromXml (elemento XElement): IAuthenticatedEncryptorDescriptor

O método ImportFromXml usa o XElement que foi retornado por [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) e cria um equivalente ao IAuthenticatedEncryptorDescriptor original.

Os tipos que implementam IAuthenticatedEncryptorDescriptorDeserializer devem ter um dos dois construtores públicos a seguir:

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> O IServiceProvider passado para o construtor pode ser nulo.

## <a name="the-top-level-factory"></a>A fábrica de nível superior

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A classe **AlgorithmConfiguration** representa um tipo que sabe como criar instâncias [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Ele expõe uma única API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Considere o AlgorithmConfiguration como a fábrica de nível superior. A configuração serve como um modelo. Ele encapsula informações de algoritmos (por exemplo, essa configuração produz descritores com uma chave mestra AES-128-GCM), mas ainda não está associado a uma chave específica.

Quando CreateNewDescriptor é chamado, o material de chave atualizado é criado exclusivamente para essa chamada, e um novo IAuthenticatedEncryptorDescriptor é produzido, o que encapsula esse material da chave e as informações de algoritmos necessárias para consumir o material. O material da chave pode ser criado no software (e mantido na memória), ele pode ser criado e mantido em um HSM e assim por diante. O ponto crucial é que quaisquer duas chamadas para CreateNewDescriptor nunca devem criar instâncias IAuthenticatedEncryptorDescriptor equivalentes.

O tipo AlgorithmConfiguration serve como o ponto de entrada para rotinas de criação de chave, como a [interrupção automática de chave](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Para alterar a implementação de todas as chaves futuras, defina a propriedade AuthenticatedEncryptorConfiguration em keymanagementoptions.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

A interface **IAuthenticatedEncryptorConfiguration** representa um tipo que sabe como criar instâncias [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Ele expõe uma única API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Considere o IAuthenticatedEncryptorConfiguration como a fábrica de nível superior. A configuração serve como um modelo. Ele encapsula informações de algoritmos (por exemplo, essa configuração produz descritores com uma chave mestra AES-128-GCM), mas ainda não está associado a uma chave específica.

Quando CreateNewDescriptor é chamado, o material de chave atualizado é criado exclusivamente para essa chamada, e um novo IAuthenticatedEncryptorDescriptor é produzido, o que encapsula esse material da chave e as informações de algoritmos necessárias para consumir o material. O material da chave pode ser criado no software (e mantido na memória), ele pode ser criado e mantido em um HSM e assim por diante. O ponto crucial é que quaisquer duas chamadas para CreateNewDescriptor nunca devem criar instâncias IAuthenticatedEncryptorDescriptor equivalentes.

O tipo IAuthenticatedEncryptorConfiguration serve como o ponto de entrada para rotinas de criação de chave, como a [interrupção automática de chave](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Para alterar a implementação de todas as chaves futuras, registre um singleton IAuthenticatedEncryptorConfiguration no contêiner de serviço.

---
