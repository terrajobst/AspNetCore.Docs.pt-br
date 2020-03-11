---
title: Detalhes de criptografia autenticados no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação da criptografia autenticada da proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667758"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Detalhes de criptografia autenticados no ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Chamadas para IDataProtector. Protect são operações de criptografia autenticadas. O método Protect oferece confidencialidade e autenticidade e está vinculado à cadeia de finalidade que foi usada para derivar essa instância IDataProtector específica de seu IDataProtectionProvider raiz.

IDataProtector. Protect usa um parâmetro de texto não criptografado de byte [] e produz uma carga protegida de byte [], cujo formato é descrito abaixo. (Há também uma sobrecarga de método de extensão que usa um parâmetro de cadeia de caracteres de texto não criptografado e retorna uma carga protegida por cadeia de caracteres. Se essa API for usada, o formato da carga protegida ainda terá a estrutura abaixo, mas será [codificado em base64url](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Formato da carga protegida

O formato da carga protegida consiste em três componentes principais:

* Um cabeçalho mágico de 32 bits que identifica a versão do sistema de proteção de dados.

* Uma ID de chave de 128 bits que identifica a chave usada para proteger essa carga específica.

* O restante da carga protegida é [específico do criptografador encapsulado por essa chave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). No exemplo a seguir, a chave representa um criptografador AES-256-CBC + HMACSHA256 e a carga é ainda mais subdividida da seguinte maneira:
  * Um modificador de chave de 128 bits.
  * Um vetor de inicialização de 128 bits.
  * 48 bytes de saída AES-256-CBC.
  * Uma marca de autenticação HMACSHA256.

Um conteúdo protegido de exemplo é ilustrado abaixo.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

Do formato de carga acima dos primeiros 32 bits, ou 4 bytes são o cabeçalho mágico que identifica a versão (09 F0 C9 F0)

Os próximos 128 bits ou 16 bytes é o identificador de chave (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF 57 EE)

O resto contém a carga e é específico do formato usado.

> [!WARNING]
> Todas as cargas protegidas a uma determinada chave começarão com o mesmo cabeçalho de 20 bytes (valor mágico, ID da chave). Os administradores podem usar esse fato para fins de diagnóstico aproximados quando uma carga foi gerada. Por exemplo, a carga acima corresponde à chave {0c819c80-6619-4019-9536-53f8aaffee57}. Se, depois de verificar o repositório de chaves, você descobrir que essa data de ativação da chave específica foi 2015-01-01 e sua data de expiração foi 2015-03-01, é razoável pressupor que a carga (se não violada) foi gerada dentro dessa janela, dar ou levar um pequeno adequações fator em ambos os lados.
