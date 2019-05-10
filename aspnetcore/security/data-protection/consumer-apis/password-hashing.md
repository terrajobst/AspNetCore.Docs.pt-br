---
title: Hash de senhas no ASP.NET Core
author: rick-anderson
description: Saiba como o hash de senhas usando as APIs de proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 70301ffffbaaf3c5ff0642b19b80e40be83aa438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896643"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="42fd6-103">Hash de senhas no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42fd6-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="42fd6-104">A base de código do proteção de dados inclui um pacote *Microsoft.AspNetCore.Cryptography.KeyDerivation* que contém funções de derivação de chave de criptografia.</span><span class="sxs-lookup"><span data-stu-id="42fd6-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="42fd6-105">Esse pacote é um componente autônomo e não tem nenhuma dependência no restante do sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="42fd6-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="42fd6-106">Ele pode ser usado independentemente completamente.</span><span class="sxs-lookup"><span data-stu-id="42fd6-106">It can be used completely independently.</span></span> <span data-ttu-id="42fd6-107">A origem existe junto com o código de proteção de dados base como uma conveniência.</span><span class="sxs-lookup"><span data-stu-id="42fd6-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="42fd6-108">O pacote no momento, oferece um método `KeyDerivation.Pbkdf2` que permite o hash de uma senha usando o [PBKDF2 algoritmo](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="42fd6-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="42fd6-109">Essa API é muito semelhante à existente do .NET Framework [Rfc2898DeriveBytes tipo](/dotnet/api/system.security.cryptography.rfc2898derivebytes), mas há três diferenças importantes:</span><span class="sxs-lookup"><span data-stu-id="42fd6-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="42fd6-110">O `KeyDerivation.Pbkdf2` método dá suporte ao consumo PRFs vários (atualmente `HMACSHA1`, `HMACSHA256`, e `HMACSHA512`), enquanto o `Rfc2898DeriveBytes` digite dá suporte apenas à `HMACSHA1`.</span><span class="sxs-lookup"><span data-stu-id="42fd6-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="42fd6-111">O `KeyDerivation.Pbkdf2` método detecta o sistema operacional atual e tenta escolher a implementação mais otimizada de rotina, fornecendo um desempenho muito melhor em alguns casos.</span><span class="sxs-lookup"><span data-stu-id="42fd6-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="42fd6-112">(No Windows 8, ele oferece cerca de 10 vezes a taxa de transferência `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="42fd6-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="42fd6-113">O `KeyDerivation.Pbkdf2` método requer que o chamador especificar todos os parâmetros (salt, PRF e contagem de iteração).</span><span class="sxs-lookup"><span data-stu-id="42fd6-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="42fd6-114">O `Rfc2898DeriveBytes` tipo fornece valores padrão para eles.</span><span class="sxs-lookup"><span data-stu-id="42fd6-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="42fd6-115">Consulte a [código-fonte](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) para o ASP.NET Core Identity `PasswordHasher` caso de uso de tipo para um mundo real.</span><span class="sxs-lookup"><span data-stu-id="42fd6-115">See the [source code](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
