---
title: APIs de proteção de dados de diversos ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a interface ISecret de proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896613"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>APIs de proteção de dados de diversos ASP.NET Core

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.

## <a name="isecret"></a>ISecret

O `ISecret` interface representa um valor de segredo, como material de chave de criptografia. Ele contém a superfície de API a seguir:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

O `WriteSecretIntoBuffer` método preenche o buffer fornecido com o valor bruto do segredo. O motivo pelo qual essa API usa o buffer como um parâmetro em vez de retornar um `byte[]` diretamente é que isso o chamador tem a oportunidade para fixar o objeto de buffer, limitando a exposição secreta para o coletor de lixo gerenciada.

O `Secret` tipo é uma implementação concreta de `ISecret` onde o valor do segredo é armazenado na memória no processo. Em plataformas Windows, o valor do segredo é criptografado por meio [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).
