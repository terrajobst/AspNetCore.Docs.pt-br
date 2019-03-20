---
title: Formato de armazenamento de chaves no ASP.NET Core
author: rick-anderson
description: Conheça os detalhes de implementação do formato de armazenamento de chaves de proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208012"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formato de armazenamento de chaves no ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Objetos são armazenados em repouso na representação XML. O diretório padrão para o armazenamento de chaves é % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.

## <a name="the-key-element"></a>O \<key > elemento

Chaves existem como objetos de nível superior no repositório de chave. Por convenção, as chaves têm o nome do arquivo **chave-{guid}. XML**, onde {guid} é a id da chave. Cada arquivo contém uma única chave. O formato do arquivo é da seguinte maneira.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

O \<key > elemento contém os seguintes atributos e elementos filho:

* A id da chave. Esse valor é tratado como autoritativo; o nome do arquivo é simplesmente uma sutileza por humanos.

* A versão do \<key > elemento, no momento é fixado em 1.

* Datas de criação, ativação e expiração da chave.

* Um \<descritor > elemento, que contém informações sobre a implementação de criptografia autenticada contida dentro dessa chave.

No exemplo acima, id da chave é {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, ele foi criado e ativado no dia 19 de março de 2015, e ele tem um tempo de vida de 90 dias. (Ocasionalmente, a data de ativação pode ser um pouco antes da data de criação, como neste exemplo. Isso ocorre devido a um nit em como as APIs funcionam e é inofensivas na prática.)

## <a name="the-descriptor-element"></a>O \<descritor > elemento

Externo \<descritor > elemento contém um deserializerType de atributo, que é o nome qualificado pelo assembly de um tipo que implementa IAuthenticatedEncryptorDescriptorDeserializer. Esse tipo é responsável por ler interno \<descritor > elemento e para analisar as informações contidas neles.

O formato específico do \<descritor > elemento depende da implementação do Criptografador autenticado encapsulada pela chave e cada tipo de desserializador espera um formato ligeiramente diferente para isso. Em geral, no entanto, esse elemento conterá informações algorítmicos (nomes, tipos, OIDs, ou semelhante) e o material de chave secreta. No exemplo acima, o descritor especifica que essa chave encapsula a criptografia AES-256-CBC + HMACSHA256 validação.

## <a name="the-encryptedsecret-element"></a>O \<encryptedSecret > elemento

Uma **&lt;encryptedSecret&gt;** elemento que contém o formulário criptografado do material de chave secreto pode estar presente se [criptografia de segredos em repouso está ativada](xref:security/data-protection/implementation/key-encryption-at-rest). O atributo `decryptorType` é o nome qualificado pelo assembly de um tipo que implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Esse tipo é responsável por ler interna **&lt;encryptedKey&gt;** elemento e descriptografá-los para recuperar o texto sem formatação original.

Assim como acontece com `<descriptor>`, o formato específico do `<encryptedSecret>` elemento depende do mecanismo de criptografia em repouso em uso. No exemplo acima, a chave mestra é criptografada usando a DPAPI do Windows por comentário.

## <a name="the-revocation-element"></a>O \<revogação > elemento

Revogações existem como objetos de nível superior no repositório de chave. Por convenção revogações têm o nome do arquivo **revogação-{timestamp}. XML** (para revogar todas as chaves antes de uma data específica) ou **revogação-{guid}. XML** (para a revogação de uma chave específica). Cada arquivo contém um único \<revogação > elemento.

Para revogações de chaves individuais, o conteúdo do arquivo será conforme mostrado abaixo.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

Nesse caso, apenas a chave especificada é revogada. Se a id da chave é "*", no entanto, como mostra o exemplo abaixo, cuja data de criação está antes da data de revogação especificado de todas as chaves são revogadas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

O \<motivo > elemento nunca é lido pelo sistema. Ele é simplesmente um local conveniente para armazenar um motivo legível por humanos para revogação.
