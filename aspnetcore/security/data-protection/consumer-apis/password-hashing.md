---
title: Senhas de hash no ASP.NET Core
author: rick-anderson
description: Saiba como usar hash de senhas usando o ASP.NET Core APIs de proteção de dados.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656047"
---
# <a name="hash-passwords-in-aspnet-core"></a>Senhas de hash no ASP.NET Core

A base de código de proteção de dados inclui um pacote *Microsoft. AspNetCore. Cryptography. Keyderivation* que contém funções de derivação de chave de criptografia. Este pacote é um componente autônomo e não tem dependências no restante do sistema de proteção de dados. Ele pode ser usado completamente de forma independente. A origem existe junto com a base de código de proteção de dados como uma conveniência.

Atualmente, o pacote oferece um método `KeyDerivation.Pbkdf2` que permite o hash de uma senha usando o [algoritmo PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Essa API é muito semelhante ao [tipo de Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)existente da .NET Framework, mas há três distinções importantes:

1. O método `KeyDerivation.Pbkdf2` dá suporte ao consumo de vários PRFs (atualmente `HMACSHA1`, `HMACSHA256`e `HMACSHA512`), enquanto o tipo de `Rfc2898DeriveBytes` só dá suporte a `HMACSHA1`.

2. O método `KeyDerivation.Pbkdf2` detecta o sistema operacional atual e tenta escolher a implementação mais otimizada da rotina, fornecendo um desempenho muito melhor em determinados casos. (No Windows 8, ele oferece cerca de 10 vezes a taxa de transferência de `Rfc2898DeriveBytes`.)

3. O método `KeyDerivation.Pbkdf2` requer que o chamador especifique todos os parâmetros (Salt, PRF e contagem de iteração). O tipo de `Rfc2898DeriveBytes` fornece valores padrão para eles.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Consulte o [código-fonte](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) para ASP.NET Core tipo de `PasswordHasher` da identidade para um caso de uso do mundo real.
