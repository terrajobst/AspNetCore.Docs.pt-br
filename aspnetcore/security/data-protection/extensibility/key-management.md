---
title: Extensibilidade de gerenciamento de chaves no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a extensibilidade do gerenciamento de chaves de proteção de dados do ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896903"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Extensibilidade de gerenciamento de chaves no ASP.NET Core

> [!TIP]
> Leia as [gerenciamento de chaves](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) seção antes de ler esta seção, pois ele explica alguns dos conceitos fundamentais dessas APIs.

> [!WARNING]
> Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.

## <a name="key"></a>Chave

O `IKey` interface é a representação básica de uma chave no sistema de criptografia. A chave do termo é usada aqui no sentido abstrato, não no sentido literal "criptográfico do material da chave". Uma chave tem as seguintes propriedades:

* Datas de expiração, criação e ativação

* Status de revogação

* Identificador de chave (um GUID)

::: moniker range=">= aspnetcore-2.0"

Além disso, `IKey` expõe uma `CreateEncryptor` método que pode ser usado para criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância vinculado a essa chave.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Além disso, `IKey` expõe uma `CreateEncryptorInstance` método que pode ser usado para criar um [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instância vinculado a essa chave.

::: moniker-end

> [!NOTE]
> Há uma API para recuperar o material criptográfico bruto de um `IKey` instância.

## <a name="ikeymanager"></a>IKeyManager

O `IKeyManager` interface representa um objeto responsável pelo armazenamento de chaves geral, a recuperação e a manipulação. Ele expõe três operações de alto nível:

* Criar uma nova chave e mantê-lo no armazenamento.

* Obter todas as chaves de armazenamento.

* Revogar uma ou mais chaves e persistir as informações de revogação para o armazenamento.

>[!WARNING]
> Escrevendo um `IKeyManager` é uma tarefa muito avançada e a maioria dos desenvolvedores não deve tentá-lo. Em vez disso, a maioria dos desenvolvedores deve tirar vantagem dos recursos oferecidos pelo [XmlKeyManager](#xmlkeymanager) classe.

## <a name="xmlkeymanager"></a>XmlKeyManager

O `XmlKeyManager` tipo é a implementação concreta da caixa de entrada da `IKeyManager`. Ele fornece vários recursos úteis, incluindo caução de chaves e criptografia de chaves em repouso. Chaves nesse sistema são representadas como elementos XML (especificamente, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` depende de vários outros componentes no decorrer de cumprir suas tarefas:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, que determina os algoritmos usados por novas chaves.

* `IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.

* `IXmlEncryptor` [opcional], que permite criptografar as chaves em repouso.

* `IKeyEscrowSink` [opcional], que fornece serviços de caução de chaves.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, que controla onde as chaves são mantidas no armazenamento.

* `IXmlEncryptor` [opcional], que permite criptografar as chaves em repouso.

* `IKeyEscrowSink` [opcional], que fornece serviços de caução de chaves.

::: moniker-end

Abaixo estão os diagramas de alto nível que indicam como esses componentes são vinculados dentro `XmlKeyManager`.

::: moniker range=">= aspnetcore-2.0"

![Criação da chave](key-management/_static/keycreation2.png)

*Criação de chave / CreateNewKey*

Na implementação de `CreateNewKey`, o `AlgorithmConfiguration` componente é usado para criar um único `IAuthenticatedEncryptorDescriptor`, que é então serializado como XML. Se um coletor de caução de chave estiver presente, o XML bruto de (não criptografado) é fornecido para o coletor para armazenamento de longo prazo. O XML não criptografado é executado por meio de um `IXmlEncryptor` (se necessário) para gerar o documento XML criptografado. Este documento criptografado é persistido no armazenamento de longo prazo via o `IXmlRepository`. (Se nenhum `IXmlEncryptor` é configurado, o documento não criptografado é persistido no `IXmlRepository`.)

![Recuperação de chave](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Criação da chave](key-management/_static/keycreation1.png)

*Criação de chave / CreateNewKey*

Na implementação de `CreateNewKey`, o `IAuthenticatedEncryptorConfiguration` componente é usado para criar um único `IAuthenticatedEncryptorDescriptor`, que é então serializado como XML. Se um coletor de caução de chave estiver presente, o XML bruto de (não criptografado) é fornecido para o coletor para armazenamento de longo prazo. O XML não criptografado é executado por meio de um `IXmlEncryptor` (se necessário) para gerar o documento XML criptografado. Este documento criptografado é persistido no armazenamento de longo prazo via o `IXmlRepository`. (Se nenhum `IXmlEncryptor` é configurado, o documento não criptografado é persistido no `IXmlRepository`.)

![Recuperação de chave](key-management/_static/keyretrieval1.png)

::: moniker-end

*Recuperação de chave / GetAllKeys*

Na implementação de `GetAllKeys`, o XML documenta as chaves que representa e revogações são lidas do subjacente `IXmlRepository`. Se esses documentos são criptografados, o sistema automaticamente descriptografá-los. `XmlKeyManager` cria o apropriada `IAuthenticatedEncryptorDescriptorDeserializer` instâncias para desserializar os documentos de volta para o `IAuthenticatedEncryptorDescriptor` instâncias, que, em seguida, são encapsuladas em indivíduo `IKey` instâncias. Esta coleção de `IKey` instâncias é retornado ao chamador.

Obter mais informações sobre os elementos XML específicos podem ser encontradas na [documentos de formato de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

O `IXmlRepository` interface representa um tipo que pode persistir o XML para e recuperar o XML de um armazenamento de backup. Ele expõe duas APIs:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementações de `IXmlRepository` não precisam analisar o XML passar através deles. Eles devem tratar os documentos XML como opaco e permitir que camadas superiores se preocupar sobre como gerar e analisar os documentos.

Há quatro tipos concretos internos que implementam `IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Consulte a [documentos de provedores de armazenamento de chaves](xref:security/data-protection/implementation/key-storage-providers) para obter mais informações.

Registrando um personalizado `IXmlRepository` é apropriado ao usar um armazenamento de backup diferente (por exemplo, armazenamento de tabela do Azure).

Para alterar o repositório padrão todo o aplicativo, registre um personalizado `IXmlRepository` instância:

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

O `IXmlEncryptor` interface representa um tipo que pode criptografar um elemento XML de texto sem formatação. Ela apresenta uma única API:

* Encrypt(XElement plaintextElement) : EncryptedXmlInfo

Se um serializado `IAuthenticatedEncryptorDescriptor` contém quaisquer elementos marcados como "requer criptografia", em seguida, `XmlKeyManager` executará esses elementos por meio do configurado `IXmlEncryptor`do `Encrypt` método e ele serão mantido o elemento adulterem em vez de elemento de texto sem formatação para o `IXmlRepository`. A saída a `Encrypt` método é um `EncryptedXmlInfo` objeto. Esse objeto é um wrapper que contém os dois o resultante adulterem `XElement` e o tipo que representa um `IXmlDecryptor` que pode ser usado para decifrar o elemento correspondente.

Há quatro tipos concretos internos que implementam `IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Consulte a [criptografia de chave em documentos do rest](xref:security/data-protection/implementation/key-encryption-at-rest) para obter mais informações.

Para alterar o mecanismo padrão de chave criptografia em repouso todo o aplicativo, registre um personalizado `IXmlEncryptor` instância:

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

O `IXmlDecryptor` interface representa um tipo que sabe como descriptografar um `XElement` que foi adulterem por meio de um `IXmlEncryptor`. Ela apresenta uma única API:

* Descriptografe (encryptedElement XElement): XElement

O `Decrypt` método desfaz a criptografia executada pelo `IXmlEncryptor.Encrypt`. Em geral, cada concreto `IXmlEncryptor` implementação terá um concreto correspondente `IXmlDecryptor` implementação.

Tipos que implementam `IXmlDecryptor` deve ter um dos dois construtores públicos a seguir:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> O `IServiceProvider` passado para o construtor pode ser nulo.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

O `IKeyEscrowSink` interface representa um tipo que pode executar a caução de informações confidenciais. Lembre-se de que descritores serializados podem conter informações confidenciais (por exemplo, o material criptográfico), e esse é o que levou à introdução do [IXmlEncryptor](#ixmlencryptor) digite em primeiro lugar. No entanto, acidentes acontecem e anéis de chave podem ser excluídos ou corrompidos.

A interface de caução fornece uma hachura de escape de emergência, permitindo o acesso ao XML bruto serializado antes que ele é transformado por qualquer configurados [IXmlEncryptor](#ixmlencryptor). A interface expõe uma única API:

* Store (keyId Guid, o elemento de XElement)

Cabe ao `IKeyEscrowSink` implementação para lidar com o elemento fornecido de maneira segura consistente com a política de negócios. Uma possível implementação pode ser para o coletor de caução criptografar o elemento XML usando um certificado x. 509 corporativo reconhecido onde chave privada do certificado tenha sido mantida em garantia; o `CertificateXmlEncryptor` tipo pode ajudar com isso. O `IKeyEscrowSink` implementação também é responsável por manter o elemento fornecido adequadamente.

Por padrão nenhum mecanismo de caução está habilitado, embora os administradores de servidor podem [configurar isso globalmente](xref:security/data-protection/configuration/machine-wide-policy). Ele também pode ser configurado por meio de programação por meio de `IDataProtectionBuilder.AddKeyEscrowSink` método conforme mostrado no exemplo a seguir. O `AddKeyEscrowSink` espelho de sobrecargas do método de `IServiceCollection.AddSingleton` e `IServiceCollection.AddInstance` sobrecargas, como `IKeyEscrowSink` instâncias devem ser singletons. Se vários `IKeyEscrowSink` instâncias registradas, cada um deles será chamado durante a geração de chave, para que as chaves podem ser mantida em garantia para diversos mecanismos simultaneamente.

Há uma API para ler o material de um `IKeyEscrowSink` instância. Isso é consistente com a teoria de design do mecanismo de caução: ele deve disponibilizar o material da chave para uma autoridade confiável, e uma vez que o aplicativo em si não é uma autoridade confiável, ele não deve ter acesso ao seu próprio caucionada material.

O código de exemplo a seguir demonstra como criar e registrar um `IKeyEscrowSink` onde as chaves são mantida em garantia, de modo que somente os membros do grupo "administradores de CONTOSODomain" poderá recuperá-los.

> [!NOTE]
> Para executar este exemplo, você deve estar em um domínio do Windows 8 / máquina Windows Server 2012 e o controlador de domínio devem ser Windows Server 2012 ou posterior.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
