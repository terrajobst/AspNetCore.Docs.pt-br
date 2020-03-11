---
title: Formato de armazenamento de chave no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação do formato de armazenamento de chave de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667751"
---
# <a name="key-storage-format-in-aspnet-core"></a>Formato de armazenamento de chave no ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Os objetos são armazenados em repouso na representação XML. O diretório padrão para o armazenamento de chaves é%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.

## <a name="the-key-element"></a>O elemento de > de chave de \<

As chaves existem como objetos de nível superior no repositório de chaves. Por chaves de Convenção têm a **chave filename-{GUID}. xml**, em que {GUID} é a ID da chave. Cada arquivo desse tipo contém uma única chave. O formato do arquivo é o seguinte.

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

O elemento de > de chave \<contém os seguintes atributos e elementos filho:

* A ID da chave. Esse valor é tratado como autoritativo; o nome do arquivo é simplesmente um sutileza para legibilidade humana.

* A versão do elemento \<Key >, atualmente fixada em 1.

* A criação, a ativação e as datas de expiração da chave.

* Um \<descritor > elemento, que contém informações sobre a implementação de criptografia autenticada contida nessa chave.

No exemplo acima, a ID da chave é {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, foi criada e ativada em 19 de março de 2015 e tem um tempo de vida de 90 dias. (Ocasionalmente, a data de ativação pode ser um pouco antes da data de criação, como neste exemplo. Isso se deve a um nit em como as APIs funcionam e é inofensiva na prática.)

## <a name="the-descriptor-element"></a>O descritor de \<> elemento

O elemento > do descritor de \<externo contém um deserializador de atributo, que é o nome qualificado pelo assembly de um tipo que implementa IAuthenticatedEncryptorDescriptorDeserializer. Esse tipo é responsável por ler o elemento > do descritor de \<interno e para analisar as informações contidas no.

O formato específico do elemento > do descritor de \<depende da implementação do criptografador autenticado encapsulado pela chave, e cada tipo de desserializador espera um formato ligeiramente diferente para isso. Em geral, no entanto, esse elemento conterá informações de algoritmos (nomes, tipos, OIDs ou semelhantes) e material de chave secreta. No exemplo acima, o descritor especifica que essa chave encapsula criptografia AES-256-CBC + validação de HMACSHA256.

## <a name="the-encryptedsecret-element"></a>O elemento \<encryptedSecret >

Um **&lt;encryptedSecret&gt;** elemento que contém a forma criptografada do material da chave secreta pode estar presente se [a criptografia de segredos em repouso estiver habilitada](xref:security/data-protection/implementation/key-encryption-at-rest). O atributo `decryptorType` é o nome qualificado para assembly de um tipo que implementa [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Esse tipo é responsável por ler o elemento interno **&lt;encryptedKey&gt;** e descriptografá-lo para recuperar o texto não criptografado original.

Assim como ocorre com `<descriptor>`, o formato específico do elemento `<encryptedSecret>` depende do mecanismo de criptografia em repouso em uso. No exemplo acima, a chave mestra é criptografada usando o Windows DPAPI de acordo com o comentário.

## <a name="the-revocation-element"></a>O elemento de > de revogação \<

As revogações existem como objetos de nível superior no repositório de chaves. Por revogações de Convenção têm a revogação de nome de arquivo **-{timestamp}. xml** (para revogar todas as chaves antes de uma data específica) ou **revogação-{GUID}. xml** (para revogar uma chave específica). Cada arquivo contém um único elemento de > de revogação de \<.

Para revogações de chaves individuais, o conteúdo do arquivo será o seguinte.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

Nesse caso, apenas a chave especificada é revogada. No entanto, se a ID da chave for "*", como no exemplo abaixo, todas as chaves cuja data de criação é anterior à data de revogação especificada serão revogadas.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

O \<motivo > elemento nunca é lido pelo sistema. É simplesmente um local conveniente para armazenar um motivo legível por humanos para revogação.
