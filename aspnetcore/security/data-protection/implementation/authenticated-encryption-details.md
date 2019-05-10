---
title: Detalhes de criptografia autenticada no ASP.NET Core
author: rick-anderson
description: Saiba os detalhes de implementação de criptografia da proteção de dados do ASP.NET Core autenticado.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896623"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="ce89e-103">Detalhes de criptografia autenticada no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce89e-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="ce89e-104">Chamadas para IDataProtector.Protect são operações de criptografia autenticada.</span><span class="sxs-lookup"><span data-stu-id="ce89e-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="ce89e-105">O método Protect oferece confidencialidade e a autenticidade e ela é vinculada à cadeia finalidade que foi usada para essa instância específica do IDataProtector derivam da sua raiz IDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="ce89e-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="ce89e-106">IDataProtector.Protect assume um parâmetro de texto sem formatação do byte [] e produz um byte [] protegido conteúdo, cujo formato é descrito abaixo.</span><span class="sxs-lookup"><span data-stu-id="ce89e-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="ce89e-107">(Também há uma sobrecarga de método de extensão que usa um parâmetro de texto sem formatação da cadeia de caracteres e retorna um conteúdo protegido de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ce89e-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="ce89e-108">Se essa API é usada ainda terá o formato do conteúdo protegido a abaixo da estrutura, mas ele estará [base64url codificado](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="ce89e-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="ce89e-109">Formato do conteúdo protegido</span><span class="sxs-lookup"><span data-stu-id="ce89e-109">Protected payload format</span></span>

<span data-ttu-id="ce89e-110">O formato do conteúdo protegido consiste em três componentes principais:</span><span class="sxs-lookup"><span data-stu-id="ce89e-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="ce89e-111">Um cabeçalho mágico de 32 bits que identifica a versão do sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="ce89e-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="ce89e-112">Uma id chave de 128 bits que identifica a chave usada para proteger essa carga específica.</span><span class="sxs-lookup"><span data-stu-id="ce89e-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="ce89e-113">É o restante da carga protegida [específicas para o Criptografador encapsulado por essa chave](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="ce89e-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="ce89e-114">No exemplo a seguir, a chave representa um AES-256-CBC + Criptografador HMACSHA256, e a carga é subdividida da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="ce89e-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="ce89e-115">Um modificador de chave de 128 bits.</span><span class="sxs-lookup"><span data-stu-id="ce89e-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="ce89e-116">Um vetor de inicialização de 128 bits.</span><span class="sxs-lookup"><span data-stu-id="ce89e-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="ce89e-117">48 bytes de saída do AES-256-CBC.</span><span class="sxs-lookup"><span data-stu-id="ce89e-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="ce89e-118">Uma marca de autenticação HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="ce89e-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="ce89e-119">Um exemplo de conteúdo protegido é ilustrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="ce89e-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="ce89e-120">Do formato de carga acima os primeiros 32 bits ou 4 bytes são o cabeçalho de mágico que identifica a versão (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="ce89e-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="ce89e-121">O próximo 128 bits ou 16 bytes é o identificador de chave (80 9 81 de C 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="ce89e-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="ce89e-122">O restante contém a carga e é específico para o formato usado.</span><span class="sxs-lookup"><span data-stu-id="ce89e-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="ce89e-123">Todas as cargas protegidas para uma determinada chave serão iniciado com o mesmo cabeçalho de 20 bytes (valor mágico, id da chave).</span><span class="sxs-lookup"><span data-stu-id="ce89e-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="ce89e-124">Os administradores podem usar esse fato para fins de diagnóstico para aproximar quando um conteúdo que foi gerado.</span><span class="sxs-lookup"><span data-stu-id="ce89e-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="ce89e-125">Por exemplo, a carga acima corresponde à chave {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="ce89e-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="ce89e-126">Se depois de verificar se o repositório de chaves, você achar que data de ativação dessa chave específica foi 2015-01-01 e sua data de validade era 2015-03-01, então é razoável pressupor que a carga (se não adulterado) foi gerado dentro dessa janela, forneça ou levar a um pequeno fator em ambos os lados.</span><span class="sxs-lookup"><span data-stu-id="ce89e-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
