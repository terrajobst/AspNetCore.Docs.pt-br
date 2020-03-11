---
title: Diversas APIs de proteção de dados de ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a interface ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666078"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="31d86-103">Diversas APIs de proteção de dados de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31d86-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="31d86-104">Tipos que implementam qualquer uma das seguintes interfaces devem ser thread-safe para chamadores vários.</span><span class="sxs-lookup"><span data-stu-id="31d86-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="31d86-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="31d86-105">ISecret</span></span>

<span data-ttu-id="31d86-106">A interface `ISecret` representa um valor secreto, como o material de chave de criptografia.</span><span class="sxs-lookup"><span data-stu-id="31d86-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="31d86-107">Ele contém a seguinte superfície de API:</span><span class="sxs-lookup"><span data-stu-id="31d86-107">It contains the following API surface:</span></span>

* <span data-ttu-id="31d86-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="31d86-108">`Length`: `int`</span></span>

* <span data-ttu-id="31d86-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="31d86-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="31d86-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="31d86-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="31d86-111">O método `WriteSecretIntoBuffer` popula o buffer fornecido com o valor de segredo bruto.</span><span class="sxs-lookup"><span data-stu-id="31d86-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="31d86-112">O motivo pelo qual essa API usa o buffer como um parâmetro em vez de retornar um `byte[]` diretamente é que isso dá ao chamador a oportunidade de fixar o objeto de buffer, limitando a exposição do segredo ao coletor de lixo gerenciado.</span><span class="sxs-lookup"><span data-stu-id="31d86-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="31d86-113">O tipo de `Secret` é uma implementação concreta de `ISecret` em que o valor secreto é armazenado na memória em processo.</span><span class="sxs-lookup"><span data-stu-id="31d86-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="31d86-114">Em plataformas Windows, o valor secreto é criptografado por meio de [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span><span class="sxs-lookup"><span data-stu-id="31d86-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
