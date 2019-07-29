---
title: Senhas de hash no ASP.NET Core
author: rick-anderson
description: Saiba como usar hash de senhas usando o ASP.NET Core APIs de proteção de dados.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: bd4b8fcf6a5a16a86ada97bbd3519f872d1417b7
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602460"
---
# <a name="hash-passwords-in-aspnet-core"></a>Senhas de hash no ASP.NET Core

A base de código de proteção de dados inclui um pacote *Microsoft. AspNetCore. Cryptography.* keyderivation que contém funções de derivação de chave de criptografia. Este pacote é um componente autônomo e não tem dependências no restante do sistema de proteção de dados. Ele pode ser usado completamente de forma independente. A origem existe junto com a base de código de proteção de dados como uma conveniência.

Atualmente, o pacote oferece um `KeyDerivation.Pbkdf2` método que permite o hash de uma senha usando o [algoritmo PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Essa API é muito semelhante ao [tipo de Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)existente da .NET Framework, mas há três distinções importantes:

1. O `KeyDerivation.Pbkdf2` método dá suporte ao consumo de vários `HMACSHA1`PRFs `HMACSHA256`(atualmente `HMACSHA512`, e), `Rfc2898DeriveBytes` enquanto o tipo `HMACSHA1`só dá suporte a.

2. O `KeyDerivation.Pbkdf2` método detecta o sistema operacional atual e tenta escolher a implementação mais otimizada da rotina, fornecendo um desempenho muito melhor em determinados casos. (No Windows 8, ele oferece cerca de 10 vezes a `Rfc2898DeriveBytes`taxa de transferência de.)

3. O `KeyDerivation.Pbkdf2` método requer que o chamador especifique todos os parâmetros (Salt, PRF e contagem de iteração). O `Rfc2898DeriveBytes` tipo fornece valores padrão para eles.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Consulte o [código-fonte](https://github.com/aspnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) para obter ASP.NET Core `PasswordHasher` tipo de identidade para um caso de uso real.
