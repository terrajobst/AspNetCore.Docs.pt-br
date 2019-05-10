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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="e311f-103">APIs de proteção de dados de diversos ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e311f-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="e311f-104">Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.</span><span class="sxs-lookup"><span data-stu-id="e311f-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="e311f-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="e311f-105">ISecret</span></span>

<span data-ttu-id="e311f-106">O `ISecret` interface representa um valor de segredo, como material de chave de criptografia.</span><span class="sxs-lookup"><span data-stu-id="e311f-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="e311f-107">Ele contém a superfície de API a seguir:</span><span class="sxs-lookup"><span data-stu-id="e311f-107">It contains the following API surface:</span></span>

* <span data-ttu-id="e311f-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="e311f-108">`Length`: `int`</span></span>

* <span data-ttu-id="e311f-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="e311f-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="e311f-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="e311f-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="e311f-111">O `WriteSecretIntoBuffer` método preenche o buffer fornecido com o valor bruto do segredo.</span><span class="sxs-lookup"><span data-stu-id="e311f-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="e311f-112">O motivo pelo qual essa API usa o buffer como um parâmetro em vez de retornar um `byte[]` diretamente é que isso o chamador tem a oportunidade para fixar o objeto de buffer, limitando a exposição secreta para o coletor de lixo gerenciada.</span><span class="sxs-lookup"><span data-stu-id="e311f-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="e311f-113">O `Secret` tipo é uma implementação concreta de `ISecret` onde o valor do segredo é armazenado na memória no processo.</span><span class="sxs-lookup"><span data-stu-id="e311f-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="e311f-114">Em plataformas Windows, o valor do segredo é criptografado por meio [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="e311f-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
