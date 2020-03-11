---
title: Extensibilidade de gerenciamento de chaves no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a extensibilidade do gerenciamento de chaves de proteção de dados do ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665875"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Extensibilidade de gerenciamento de chaves no ASP.NET Core

> [!TIP]
> Leia a seção [Gerenciamento de chaves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) antes de ler esta seção, pois ela explica alguns dos conceitos fundamentais por trás dessas APIs.

> [!WARNING]
> Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.

## <a name="key"></a>Chave

A interface `IKey` é a representação básica de uma chave em cryptosystem. A chave do termo é usada aqui no sentido abstrato, não no sentido literal "criptográfico do material da chave". Uma chave tem as seguintes propriedades:

* Datas de expiração, criação e ativação

* Status de revogação

* Identificador de chave (um GUID)

::: moniker range=">= aspnetcore-2.0"

Além disso, `IKey` expõe um método `CreateEncryptor` que pode ser usado para criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) vinculada a essa chave.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Além disso, `IKey` expõe um método `CreateEncryptorInstance` que pode ser usado para criar uma instância [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) vinculada a essa chave.

::: moniker-end

> [!NOTE]
> Não há API para recuperar o material de criptografia bruto de uma instância de `IKey`.

## <a name="ikeymanager"></a>IKeyManager

A interface `IKeyManager` representa um objeto responsável por armazenamento de chave geral, recuperação e manipulação. Ele expõe três operações de alto nível:

* Criar uma nova chave e mantê-lo no armazenamento.

* Obter todas as chaves de armazenamento.

* Revogar uma ou mais chaves e persistir as informações de revogação para o armazenamento.

>[!WARNING]
> Escrever um `IKeyManager` é uma tarefa muito avançada, e a maioria dos desenvolvedores não deve tentar. Em vez disso, a maioria dos desenvolvedores deve aproveitar as facilidades oferecidas pela classe [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

O tipo de `XmlKeyManager` é a implementação concreta da caixa de `IKeyManager`. Ele fornece vários recursos úteis, incluindo caução de chaves e criptografia de chaves em repouso. As chaves neste sistema são representadas como elementos XML (especificamente, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` depende de vários outros componentes no decorrer do cumprimento de suas tarefas:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, que determina os algoritmos usados por novas chaves.

* `IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.

* `IXmlEncryptor` [opcional], que permite criptografar chaves em repouso.

* `IKeyEscrowSink` [opcional], que fornece serviços de caução de chave.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.

* `IXmlEncryptor` [opcional], que permite criptografar chaves em repouso.

* `IKeyEscrowSink` [opcional], que fornece serviços de caução de chave.

::: moniker-end

Abaixo estão os diagramas de alto nível que indicam como esses componentes são conectados em `XmlKeyManager`.

::: moniker range=">= aspnetcore-2.0"

![Criação da chave](key-management/_static/keycreation2.png)

*Criação de chave/CreateNewKey*

Na implementação de `CreateNewKey`, o componente `AlgorithmConfiguration` é usado para criar um `IAuthenticatedEncryptorDescriptor`exclusivo, que é serializado como XML. Se um coletor de caução de chave estiver presente, o XML bruto de (não criptografado) é fornecido para o coletor para armazenamento de longo prazo. Em seguida, o XML não criptografado é executado por meio de um `IXmlEncryptor` (se necessário) para gerar o documento XML criptografado. Esse documento criptografado é mantido para armazenamento de longo prazo por meio do `IXmlRepository`. (Se nenhuma `IXmlEncryptor` estiver configurada, o documento não criptografado será mantido no `IXmlRepository`.)

![Recuperação de chave](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Criação da chave](key-management/_static/keycreation1.png)

*Criação de chave/CreateNewKey*

Na implementação de `CreateNewKey`, o componente `IAuthenticatedEncryptorConfiguration` é usado para criar um `IAuthenticatedEncryptorDescriptor`exclusivo, que é serializado como XML. Se um coletor de caução de chave estiver presente, o XML bruto de (não criptografado) é fornecido para o coletor para armazenamento de longo prazo. Em seguida, o XML não criptografado é executado por meio de um `IXmlEncryptor` (se necessário) para gerar o documento XML criptografado. Esse documento criptografado é mantido para armazenamento de longo prazo por meio do `IXmlRepository`. (Se nenhuma `IXmlEncryptor` estiver configurada, o documento não criptografado será mantido no `IXmlRepository`.)

![Recuperação de chave](key-management/_static/keyretrieval1.png)

::: moniker-end

*Recuperação de chave/GetAllKeys*

Na implementação de `GetAllKeys`, os documentos XML que representam as chaves e revogações são lidos no `IXmlRepository`subjacente. Se esses documentos são criptografados, o sistema automaticamente descriptografá-los. `XmlKeyManager` cria as instâncias de `IAuthenticatedEncryptorDescriptorDeserializer` apropriadas para desserializar os documentos de volta em instâncias de `IAuthenticatedEncryptorDescriptor`, que são encapsuladas em instâncias individuais de `IKey`. Essa coleção de instâncias de `IKey` é retornada ao chamador.

Mais informações sobre os elementos XML específicos podem ser encontradas no [documento de formato de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

A interface `IXmlRepository` representa um tipo que pode persistir XML e recuperar XML de um repositório de backup. Ele expõe duas APIs:

* `GetAllElements`:`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

As implementações de `IXmlRepository` não precisam analisar o XML transmitindo-as. Eles devem tratar os documentos XML como opaco e permitir que camadas superiores se preocupar sobre como gerar e analisar os documentos.

Há quatro tipos concretos internos que implementam `IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Consulte o [documento provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers) para obter mais informações.

O registro de um `IXmlRepository` personalizado é apropriado ao usar um repositório de backup diferente (por exemplo, armazenamento de tabelas do Azure).

Para alterar o aplicativo do repositório padrão, registre uma instância de `IXmlRepository` personalizada:

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

A interface `IXmlEncryptor` representa um tipo que pode criptografar um elemento XML de texto não criptografado. Ela apresenta uma única API:

* Criptografar (plaintextElement XElement): EncryptedXmlInfo

Se uma `IAuthenticatedEncryptorDescriptor` serializada contiver qualquer elemento marcado como "requer criptografia", `XmlKeyManager` executará esses elementos por meio do método `Encrypt` configurado do `IXmlEncryptor`, e ele persistirá o elemento encriptografado em vez do elemento de texto sem formatação para o `IXmlRepository`. A saída do método `Encrypt` é um objeto `EncryptedXmlInfo`. Esse objeto é um wrapper que contém o `XElement` codificado resultante e o tipo que representa um `IXmlDecryptor` que pode ser usado para decifrar o elemento correspondente.

Há quatro tipos concretos internos que implementam `IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Consulte o [documento criptografia de chave em repouso](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais informações.

Para alterar o mecanismo de criptografia de chave padrão em todo o aplicativo, registre uma instância de `IXmlEncryptor` personalizada:

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

A interface `IXmlDecryptor` representa um tipo que sabe como descriptografar uma `XElement` que foi codificada por meio de uma `IXmlEncryptor`. Ela apresenta uma única API:

* Descriptografar (encryptedElement XElement): XElement

O método `Decrypt` desfaz a criptografia executada por `IXmlEncryptor.Encrypt`. Em geral, cada concreto `IXmlEncryptor` implementação terá uma implementação de `IXmlDecryptor` concreta correspondente.

Os tipos que implementam `IXmlDecryptor` devem ter um dos dois construtores públicos a seguir:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> O `IServiceProvider` passado para o construtor pode ser nulo.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

A interface `IKeyEscrowSink` representa um tipo que pode executar a caução de informações confidenciais. Lembre-se de que os descritores serializados podem conter informações confidenciais (como material criptográfico), e isso é o que levou à introdução do tipo [IXmlEncryptor](#ixmlencryptor) em primeiro lugar. No entanto, acidentes acontecem e anéis de chave podem ser excluídos ou corrompidos.

A interface de caução fornece um hachura de escape de emergência, permitindo o acesso ao XML serializado bruto antes que ele seja transformado por qualquer [IXmlEncryptor](#ixmlencryptor)configurada. A interface expõe uma única API:

* Store (keyId Guid, o elemento de XElement)

Cabe à implementação de `IKeyEscrowSink` lidar com o elemento fornecido de maneira segura consistente com a política de negócios. Uma implementação possível poderia ser para o coletor de caução criptografar o elemento XML usando um certificado X. 509 corporativo conhecido em que a chave privada do certificado tenha sido caução; o tipo de `CertificateXmlEncryptor` pode ajudar com isso. A implementação de `IKeyEscrowSink` também é responsável por persistir o elemento fornecido adequadamente.

Por padrão, nenhum mecanismo de caução está habilitado, embora os administradores de servidor possam [configurar isso globalmente](xref:security/data-protection/configuration/machine-wide-policy). Ele também pode ser configurado programaticamente por meio do método `IDataProtectionBuilder.AddKeyEscrowSink`, conforme mostrado no exemplo abaixo. O método de `AddKeyEscrowSink` sobrecarrega o `IServiceCollection.AddSingleton` e `IServiceCollection.AddInstance` sobrecargas, já que as instâncias `IKeyEscrowSink` devem ser singletons. Se várias instâncias de `IKeyEscrowSink` forem registradas, cada uma será chamada durante a geração de chave, de modo que as chaves podem ter a garantia de vários mecanismos simultaneamente.

Não há API para ler material de uma instância de `IKeyEscrowSink`. Isso é consistente com a teoria de design do mecanismo de caução: ele deve disponibilizar o material da chave para uma autoridade confiável, e uma vez que o aplicativo em si não é uma autoridade confiável, ele não deve ter acesso ao seu próprio caucionada material.

O código de exemplo a seguir demonstra como criar e registrar um `IKeyEscrowSink` em que as chaves são recriadas, de modo que somente os membros de "CONTOSODomain admins" possam recuperá-las.

> [!NOTE]
> Para executar este exemplo, você deve estar em um domínio do Windows 8 / máquina Windows Server 2012 e o controlador de domínio devem ser Windows Server 2012 ou posterior.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
