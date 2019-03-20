---
title: Extensibilidade da criptografia básica no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer e a fábrica de nível superior.
ms.author: riande
ms.date: 8/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: cf4a142992efe5b00a75285ef9ad9735fe7be411
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209064"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Extensibilidade da criptografia básica no ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

O **IAuthenticatedEncryptor** interface é o bloco de construção básico do subsistema de criptografia. Geralmente é um IAuthenticatedEncryptor por chave e a instância de IAuthenticatedEncryptor encapsula todos os materiais de chave de criptografia e algorítmicos informações necessárias para executar operações criptográficas.

Como o nome sugere, o tipo é responsável por fornecer serviços de criptografia e descriptografia autenticados. Ele expõe as APIs a seguir.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

O método Encrypt retorna um blob que inclui o texto não criptografado adulterem e uma marca de autenticação. A marca de autenticação deve abranger os dados adicionais autenticados (AAD), embora o AAD em si não precisa ser recuperado por meio a carga final. O método Decrypt valida a marca de autenticação e retorna a carga deciphered. Todas as falhas (exceto ArgumentNullException e similares) devem ser homogenized para CryptographicException.

> [!NOTE]
> A própria instância IAuthenticatedEncryptor, na verdade, não precisa conter o material da chave. Por exemplo, a implementação pudesse ser delegado para um HSM para todas as operações.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Como criar um IAuthenticatedEncryptor

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

O **IAuthenticatedEncryptorFactory** interface representa um tipo que sabe como criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância. Sua API é da seguinte maneira.

* CreateEncryptorInstance (chave IKey): IAuthenticatedEncryptor

Para qualquer determinada instância de IKey, os qualquer criptografadores autenticados criados por seu método CreateEncryptorInstance devem ser considerados equivalentes, como no exemplo de código a seguir.

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

O **IAuthenticatedEncryptorDescriptor** interface representa um tipo que sabe como criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância. Sua API é da seguinte maneira.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Como IAuthenticatedEncryptor, uma instância de IAuthenticatedEncryptorDescriptor será assumida para encapsular uma chave específica. Isso significa que para determinada instância IAuthenticatedEncryptorDescriptor, qualquer criptografadores autenticados criados por seu método CreateEncryptorInstance devem ser considerados equivalentes, como mostra o exemplo de código a seguir.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2. x somente)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

O **IAuthenticatedEncryptorDescriptor** interface representa um tipo que sabe como exportar em si para XML. Sua API é da seguinte maneira.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Serialização XML

A principal diferença entre IAuthenticatedEncryptor e IAuthenticatedEncryptorDescriptor é que o descritor sabe como criar o Criptografador e fornecê-lo com os argumentos válidos. Considere um IAuthenticatedEncryptor cuja implementação se baseia em SymmetricAlgorithm e KeyedHashAlgorithm. Trabalho do Criptografador é consumir esses tipos, mas não necessariamente sabe onde esses tipos de origem, para que ele realmente não é possível gravar uma descrição apropriada sobre como recriar em si se o aplicativo for reiniciado. O descritor atua como um nível mais alto sobre isso. Uma vez que o descritor sabe como criar a instância do Criptografador (por exemplo, ele sabe como criar os algoritmos necessários), ele pode serializar esse conhecimento no formato XML, de modo que a instância do Criptografador pode ser recriada após a redefinição de um aplicativo.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

O descritor de pode ser serializado por meio de sua rotina de ExportToXml. Essa rotina retorna um XmlSerializedDescriptorInfo que contém duas propriedades: a representação de XElement do descritor e o tipo que representa um [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) que pode ser usado para ressuscitar Esse descritor de dada a XElement correspondente.

O descritor serializado pode conter informações confidenciais, como material de chave de criptografia. O sistema de proteção de dados tem suporte interno para criptografar informações antes de ele ser mantido no armazenamento. Para tirar proveito disso, o descritor deve marcar o elemento que contém informações confidenciais com o nome do atributo "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), valor "true".

>[!TIP]
> Há um auxiliar de API para definir esse atributo. Chame o método de extensão que XElement.markasrequiresencryption() localizada no namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

Também pode haver casos em que o descritor serializado não contém informações confidenciais. Considere novamente o caso de uma chave de criptografia armazenada em um HSM. O descritor não é possível gravar o material da chave ao serializar em si, pois o HSM não expõe o material em forma de texto sem formatação. Em vez disso, o descritor pode escrever-out da versão encapsulado de chave da chave (se o HSM permite exportar dessa forma) ou o identificador exclusivo do HSM a chave.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

O **IAuthenticatedEncryptorDescriptorDeserializer** interface representa um tipo que sabe como desserializar uma instância de IAuthenticatedEncryptorDescriptor de um XElement. Ela expõe um único método:

* ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor

O método ImportFromXml leva o XElement que foi retornado por [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) e cria o equivalente a IAuthenticatedEncryptorDescriptor original.

Tipos que implementam IAuthenticatedEncryptorDescriptorDeserializer devem ter um dos dois construtores públicos a seguir:

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> O IServiceProvider passado para o construtor pode ser nulo.

## <a name="the-top-level-factory"></a>A fábrica de nível superior

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

O **AlgorithmConfiguration** classe representa um tipo que sabe como criar [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instâncias. Ele expõe uma única API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Pense AlgorithmConfiguration como a fábrica de nível superior. A configuração serve como um modelo. Ele encapsula informações algorítmicos (por exemplo, essa configuração produz descritores com uma chave mestra de AES-128-GCM), mas ele ainda não tiver associado uma chave específica.

Quando CreateNewDescriptor é chamado, material de chave novo é criado exclusivamente para essa chamada e um novo IAuthenticatedEncryptorDescriptor é produzido, que encapsula esse material da chave e as informações de algorítmicos necessária para consumir o material. O material da chave pode ser criado no software (e mantido na memória), poderia ser criado e mantido em um HSM e assim por diante. O ponto fundamental é que as duas chamadas para CreateNewDescriptor nunca devem criar instâncias de IAuthenticatedEncryptorDescriptor equivalentes.

O tipo de AlgorithmConfiguration serve como ponto de entrada para as rotinas de criação da chave como [chave automática sem interrupção](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Para alterar a implementação para todas as chaves de futuros, defina a propriedade de AuthenticatedEncryptorConfiguration em KeyManagementOptions.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

O **IAuthenticatedEncryptorConfiguration** interface representa um tipo que sabe como criar [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instâncias. Ele expõe uma única API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Pense IAuthenticatedEncryptorConfiguration como a fábrica de nível superior. A configuração serve como um modelo. Ele encapsula informações algorítmicos (por exemplo, essa configuração produz descritores com uma chave mestra de AES-128-GCM), mas ele ainda não tiver associado uma chave específica.

Quando CreateNewDescriptor é chamado, material de chave novo é criado exclusivamente para essa chamada e um novo IAuthenticatedEncryptorDescriptor é produzido, que encapsula esse material da chave e as informações de algorítmicos necessária para consumir o material. O material da chave pode ser criado no software (e mantido na memória), poderia ser criado e mantido em um HSM e assim por diante. O ponto fundamental é que as duas chamadas para CreateNewDescriptor nunca devem criar instâncias de IAuthenticatedEncryptorDescriptor equivalentes.

O tipo de IAuthenticatedEncryptorConfiguration serve como ponto de entrada para as rotinas de criação da chave como [chave automática sem interrupção](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Para alterar a implementação para todas as chaves de futuros, registre um singleton IAuthenticatedEncryptorConfiguration no contêiner de serviço.

---
