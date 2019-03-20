---
title: Cabeçalhos de contexto no ASP.NET Core
author: rick-anderson
description: Conheça os detalhes de implementação dos cabeçalhos de contexto da proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 2b8fd594672bf623d38bfae90d05a984f92ce6a3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208352"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="accfd-103">Cabeçalhos de contexto no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="accfd-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="accfd-104">Plano de fundo e a teoria</span><span class="sxs-lookup"><span data-stu-id="accfd-104">Background and theory</span></span>

<span data-ttu-id="accfd-105">No sistema de proteção de dados, uma "chave" significa autenticado de um objeto que pode fornecer serviços de criptografia.</span><span class="sxs-lookup"><span data-stu-id="accfd-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="accfd-106">Cada chave é identificado por uma id exclusiva (um GUID), e ela transmite informações algorítmicos e material entropic.</span><span class="sxs-lookup"><span data-stu-id="accfd-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="accfd-107">Ele destina-se que cada chave carregam entropia exclusiva, mas o sistema não pode impor que e estamos também precisa levar em conta para os desenvolvedores que podem alterar o anel de chave manualmente, modificando as informações de algoritmos de uma chave existente do anel de chave.</span><span class="sxs-lookup"><span data-stu-id="accfd-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="accfd-108">Para atingir nossos requisitos de segurança fornecidos nesses casos, o sistema de proteção de dados tem um conceito de [agilidade criptográfica](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), que permite que com segurança usando um único valor entropic entre vários algoritmos de criptografia.</span><span class="sxs-lookup"><span data-stu-id="accfd-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="accfd-109">A maioria dos sistemas que dão suporte a agilidade criptográfica de fazer isso, incluindo algumas informações de identificação sobre o algoritmo de dentro do payload.</span><span class="sxs-lookup"><span data-stu-id="accfd-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="accfd-110">OID do algoritmo geralmente é um bom candidato para isso.</span><span class="sxs-lookup"><span data-stu-id="accfd-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="accfd-111">No entanto, um problema que tivemos é que há várias maneiras para especificar o mesmo algoritmo: "AES" (CNG) e o gerenciado Aes, AesManaged, AesCryptoServiceProvider, AesCng e RijndaelManaged (determinados parâmetros de específicos) classes são, na verdade, tudo a mesma coisa, e seria preciso manter um mapeamento de tudo isso para o OID correto.</span><span class="sxs-lookup"><span data-stu-id="accfd-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="accfd-112">Se um desenvolvedor quiser fornecer um algoritmo personalizado (ou até mesmo outra implementação do AES!), eles teria Conte-nos sua OID.</span><span class="sxs-lookup"><span data-stu-id="accfd-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="accfd-113">Esta etapa de registro extras torna a configuração do sistema particularmente doloroso.</span><span class="sxs-lookup"><span data-stu-id="accfd-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="accfd-114">Voltar em etapas, decidimos que podemos foram se aproximando o problema na direção errada.</span><span class="sxs-lookup"><span data-stu-id="accfd-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="accfd-115">Um OID informa o que é o algoritmo, mas nós não nos importamos realmente sobre isso.</span><span class="sxs-lookup"><span data-stu-id="accfd-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="accfd-116">Se é necessário usar um único valor entropic com segurança em dois algoritmos diferentes, não é necessário para que possamos saber o que os algoritmos realmente são.</span><span class="sxs-lookup"><span data-stu-id="accfd-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="accfd-117">O que realmente nos interessa é como eles se comportam.</span><span class="sxs-lookup"><span data-stu-id="accfd-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="accfd-118">Qualquer algoritmo de criptografia de bloco simétricas decente também é uma permutação de números pseudoaleatório forte (PRP): corrija as entradas (chave, texto sem formatação do modo, IV, o encadeamento) e a saída de texto cifrado com grandes probabilidade será distinta de qualquer outra codificação de bloco simétricas considerando as mesmas entradas de algoritmo.</span><span class="sxs-lookup"><span data-stu-id="accfd-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="accfd-119">Da mesma forma, qualquer função de hash com chave decente também é uma forte pseudoaleatório PRF (função), e dado um conjunto fixo de entrada sua saída predominantemente será distinta de qualquer outra função de hash com chave.</span><span class="sxs-lookup"><span data-stu-id="accfd-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="accfd-120">Podemos usar esse conceito de alta segurança PRPs e PRFs para criar um cabeçalho de contexto.</span><span class="sxs-lookup"><span data-stu-id="accfd-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="accfd-121">Esse cabeçalho de contexto atua essencialmente como uma impressão digital do estável sobre os algoritmos em uso para qualquer operação determinada, e fornece a agilidade criptográfica necessária para o sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="accfd-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="accfd-122">Esse cabeçalho pode ser reproduzido e é usado posteriormente como parte do [processo de derivação de subchaves](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="accfd-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="accfd-123">Há duas maneiras diferentes para criar o cabeçalho de contexto, dependendo dos modos de operação dos algoritmos subjacentes.</span><span class="sxs-lookup"><span data-stu-id="accfd-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="accfd-124">Criptografia do modo CBC + autenticação HMAC</span><span class="sxs-lookup"><span data-stu-id="accfd-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="accfd-125">O cabeçalho de contexto consiste dos seguintes componentes:</span><span class="sxs-lookup"><span data-stu-id="accfd-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="accfd-126">[16 bits] O valor 00 00, que é um marcador que significa "criptografia CBC + autenticação HMAC".</span><span class="sxs-lookup"><span data-stu-id="accfd-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="accfd-127">[32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas.</span><span class="sxs-lookup"><span data-stu-id="accfd-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="accfd-128">[32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas.</span><span class="sxs-lookup"><span data-stu-id="accfd-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="accfd-129">[32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="accfd-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="accfd-130">(Atualmente, o tamanho da chave sempre corresponde o tamanho do resumo.)</span><span class="sxs-lookup"><span data-stu-id="accfd-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="accfd-131">[32 bits] O tamanho (em bytes, big-endian) resumo do algoritmo HMAC.</span><span class="sxs-lookup"><span data-stu-id="accfd-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="accfd-132">EncCBC (K_E, IV, ""), que é a saída do algoritmo de criptografia de bloco simétricas dada uma entrada de cadeia de caracteres vazia e onde o IV é um vetor de zeros.</span><span class="sxs-lookup"><span data-stu-id="accfd-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="accfd-133">A construção de K_E é descrita abaixo.</span><span class="sxs-lookup"><span data-stu-id="accfd-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="accfd-134">MAC (K_H, ""), que é a saída do algoritmo HMAC dada uma entrada de cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="accfd-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="accfd-135">A construção de K_H é descrita abaixo.</span><span class="sxs-lookup"><span data-stu-id="accfd-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="accfd-136">O ideal é que podemos passar para K_E e K_H vetores de zeros.</span><span class="sxs-lookup"><span data-stu-id="accfd-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="accfd-137">No entanto, queremos evitar a situação em que o algoritmo subjacente verifica a existência de chaves fracas antes de executar qualquer operação (especialmente DES e 3DES), que impede o usando um padrão simple ou repetitivos, como um vetor de zeros.</span><span class="sxs-lookup"><span data-stu-id="accfd-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="accfd-138">Em vez disso, usamos o NIST SP800 108 KDF no modo de contador (consulte [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 5.1 seg.) com uma chave de comprimento zero, o rótulo e o contexto e o HMACSHA512 como o PRF subjacente.</span><span class="sxs-lookup"><span data-stu-id="accfd-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="accfd-139">Derivamos | K_E | + | K_H | bytes de saída, em seguida, decompor o resultado em K_E e K_H em si.</span><span class="sxs-lookup"><span data-stu-id="accfd-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="accfd-140">Matematicamente, isso é representado da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="accfd-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="accfd-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="accfd-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="accfd-142">Exemplo: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="accfd-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="accfd-143">Por exemplo, considere o caso em que o algoritmo de criptografia de bloco simétricas é AES-192-CBC e o algoritmo de validação é HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="accfd-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="accfd-144">O sistema geraria o cabeçalho de contexto usando as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="accfd-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="accfd-145">Primeiro, permita (K_E | | K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", rótulo = "", contexto = ""), onde | K_E | = 192 bits e | K_H | = 256 bits por algoritmos especificados.</span><span class="sxs-lookup"><span data-stu-id="accfd-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="accfd-146">Isso leva a K_E = 5BB6... 21DD e K_H = A04A... 00A9 no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="accfd-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="accfd-147">Em seguida, computação Enc_CBC (K_E, IV, "") para o AES-192-CBC dado IV = 0 \* e K_E como acima.</span><span class="sxs-lookup"><span data-stu-id="accfd-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="accfd-148">result := F474B1872B3B53E4721DE19C0841DB6F</span><span class="sxs-lookup"><span data-stu-id="accfd-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="accfd-149">Em seguida, o MAC de computação (K_H, "") para HMACSHA256 determinado K_H como acima.</span><span class="sxs-lookup"><span data-stu-id="accfd-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="accfd-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span><span class="sxs-lookup"><span data-stu-id="accfd-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="accfd-151">Isso produz o cabeçalho de contexto completo abaixo:</span><span class="sxs-lookup"><span data-stu-id="accfd-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="accfd-152">Esse cabeçalho de contexto é a impressão digital do par de algoritmo de criptografia autenticada (criptografia AES-192-CBC + HMACSHA256 validação).</span><span class="sxs-lookup"><span data-stu-id="accfd-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="accfd-153">Os componentes, como descrito [acima](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) são:</span><span class="sxs-lookup"><span data-stu-id="accfd-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="accfd-154">o marcador (00 00)</span><span class="sxs-lookup"><span data-stu-id="accfd-154">the marker (00 00)</span></span>

* <span data-ttu-id="accfd-155">o comprimento da chave de criptografia de bloco (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="accfd-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="accfd-156">o tamanho de bloco de codificação de bloco (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="accfd-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="accfd-157">o comprimento da chave HMAC (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="accfd-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="accfd-158">o tamanho do resumo HMAC (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="accfd-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="accfd-159">o bloco cifrado saída PRP (F4 74 - DB 6F) e</span><span class="sxs-lookup"><span data-stu-id="accfd-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="accfd-160">a saída de HMAC PRF (D4 79 - end).</span><span class="sxs-lookup"><span data-stu-id="accfd-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="accfd-161">A criptografia do modo CBC + HMAC cabeçalho de contexto de autenticação baseia-se da mesma forma independentemente se as implementações de algoritmos são fornecidas pelo CNG do Windows ou por tipos SymmetricAlgorithm e KeyedHashAlgorithm gerenciados.</span><span class="sxs-lookup"><span data-stu-id="accfd-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="accfd-162">Isso permite que aplicativos executados em diferentes sistemas operacionais para produzir confiavelmente o mesmo cabeçalho de contexto, embora as implementações de algoritmos diferem entre sistemas operacionais.</span><span class="sxs-lookup"><span data-stu-id="accfd-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="accfd-163">(Na prática, o KeyedHashAlgorithm não precisa ser um HMAC adequado.</span><span class="sxs-lookup"><span data-stu-id="accfd-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="accfd-164">Ele pode ser qualquer tipo de algoritmo de hash com chave.)</span><span class="sxs-lookup"><span data-stu-id="accfd-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="accfd-165">Exemplo: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="accfd-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="accfd-166">Primeiro, permita (K_E | | K_H) = SP800_108_CTR (prf = HMACSHA512, key = "", rótulo = "", contexto = ""), onde | K_E | = 192 bits e | K_H | = 160 bits por algoritmos especificados.</span><span class="sxs-lookup"><span data-stu-id="accfd-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="accfd-167">Isso leva a K_E = A219... E2BB e K_H = DC4A... B464 no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="accfd-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="accfd-168">Em seguida, computação Enc_CBC (K_E, IV, "") para 3DES-192-CBC dado IV = 0 \* e K_E como acima.</span><span class="sxs-lookup"><span data-stu-id="accfd-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="accfd-169">resultado: = ABB100F81E53E10E</span><span class="sxs-lookup"><span data-stu-id="accfd-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="accfd-170">Em seguida, o MAC de computação (K_H, "") para HMACSHA1 determinado K_H como acima.</span><span class="sxs-lookup"><span data-stu-id="accfd-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="accfd-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span><span class="sxs-lookup"><span data-stu-id="accfd-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="accfd-172">Isso produz o cabeçalho de contexto total que é uma impressão digital do autenticado par de algoritmo do encryption (criptografia 3DES-192-CBC + validação HMACSHA1), mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="accfd-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="accfd-173">Os componentes são discriminados da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="accfd-173">The components break down as follows:</span></span>

* <span data-ttu-id="accfd-174">o marcador (00 00)</span><span class="sxs-lookup"><span data-stu-id="accfd-174">the marker (00 00)</span></span>

* <span data-ttu-id="accfd-175">o comprimento da chave de criptografia de bloco (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="accfd-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="accfd-176">o tamanho de bloco de codificação de bloco (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="accfd-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="accfd-177">o comprimento da chave HMAC (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="accfd-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="accfd-178">o tamanho do resumo HMAC (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="accfd-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="accfd-179">o bloco cifrado saída PRP (B1 AB - E1 0E) e</span><span class="sxs-lookup"><span data-stu-id="accfd-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="accfd-180">a saída de HMAC PRF (76 EB - end).</span><span class="sxs-lookup"><span data-stu-id="accfd-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="accfd-181">Criptografia de modo Galois/contador + autenticação</span><span class="sxs-lookup"><span data-stu-id="accfd-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="accfd-182">O cabeçalho de contexto consiste dos seguintes componentes:</span><span class="sxs-lookup"><span data-stu-id="accfd-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="accfd-183">[16 bits] O valor 00 01, o que é um marcador que significa "criptografia GCM + autenticação".</span><span class="sxs-lookup"><span data-stu-id="accfd-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="accfd-184">[32 bits] O comprimento da chave (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas.</span><span class="sxs-lookup"><span data-stu-id="accfd-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="accfd-185">[32 bits] O tamanho nonce (em bytes, big-endian) usado durante as operações de criptografia autenticada.</span><span class="sxs-lookup"><span data-stu-id="accfd-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="accfd-186">(Para nosso sistema, isso foi corrigido no tamanho de nonce = 96 bits.)</span><span class="sxs-lookup"><span data-stu-id="accfd-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="accfd-187">[32 bits] O tamanho do bloco (em bytes, big-endian) do algoritmo de criptografia de bloco simétricas.</span><span class="sxs-lookup"><span data-stu-id="accfd-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="accfd-188">(Para GCM, isso foi corrigido no tamanho do bloco de = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="accfd-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="accfd-189">[32 bits] O autenticação marca tamanho (em bytes, big-endian) produzido pela função criptografia autenticada.</span><span class="sxs-lookup"><span data-stu-id="accfd-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="accfd-190">(Para nosso sistema, isso foi corrigido no tamanho da marca = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="accfd-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="accfd-191">[128 bits] A marca de Enc_GCM (K_E, nonce, ""), que é a saída do algoritmo de criptografia de bloco simétricas dada uma entrada de cadeia de caracteres vazia e onde o nonce é um vetor de zeros de 96 bits.</span><span class="sxs-lookup"><span data-stu-id="accfd-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="accfd-192">K_E é derivado usando o mesmo mecanismo como a criptografia CBC + o cenário de autenticação do HMAC.</span><span class="sxs-lookup"><span data-stu-id="accfd-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="accfd-193">No entanto, já que não há nenhum K_H em jogo aqui, essencialmente, temos | K_H | = 0, e o algoritmo se recolhe para o abaixo do formulário.</span><span class="sxs-lookup"><span data-stu-id="accfd-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="accfd-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="accfd-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="accfd-195">Exemplo: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="accfd-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="accfd-196">Primeiro, permita K_E = SP800_108_CTR (prf = HMACSHA512, key = "", rótulo = "", contexto = ""), onde | K_E | = 256 bits.</span><span class="sxs-lookup"><span data-stu-id="accfd-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="accfd-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span><span class="sxs-lookup"><span data-stu-id="accfd-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="accfd-198">Em seguida, a marca de autenticação de Enc_GCM de computação (K_E, nonce, "") para AES-256-GCM dado nonce = 096 e K_E como acima.</span><span class="sxs-lookup"><span data-stu-id="accfd-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="accfd-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span><span class="sxs-lookup"><span data-stu-id="accfd-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="accfd-200">Isso produz o cabeçalho de contexto completo abaixo:</span><span class="sxs-lookup"><span data-stu-id="accfd-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="accfd-201">Os componentes são discriminados da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="accfd-201">The components break down as follows:</span></span>

* <span data-ttu-id="accfd-202">o marcador (00 01)</span><span class="sxs-lookup"><span data-stu-id="accfd-202">the marker (00 01)</span></span>

* <span data-ttu-id="accfd-203">o comprimento da chave de criptografia de bloco (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="accfd-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="accfd-204">o tamanho de nonce (00 00 00 0 C)</span><span class="sxs-lookup"><span data-stu-id="accfd-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="accfd-205">o tamanho de bloco de codificação de bloco (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="accfd-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="accfd-206">o tamanho da marca de autenticação (00 00 00 10) e</span><span class="sxs-lookup"><span data-stu-id="accfd-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="accfd-207">a marca de autenticação executem a codificação de bloco (controlador de domínio E7 - end).</span><span class="sxs-lookup"><span data-stu-id="accfd-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
