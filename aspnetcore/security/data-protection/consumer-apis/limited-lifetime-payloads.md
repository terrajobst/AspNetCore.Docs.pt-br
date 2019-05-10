---
title: Limite o tempo de vida de cargas protegidas no ASP.NET Core
author: rick-anderson
description: Saiba como limitar o tempo de vida de um conteúdo protegido usando as APIs de proteção de dados do ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 8dc3b856ec67477ec8ae777749c9bf3107eb4eda
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897113"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Limite o tempo de vida de cargas protegidas no ASP.NET Core

Há cenários em que o desenvolvedor de aplicativos deseja criar um conteúdo protegido que expira após um período de tempo definido. Por exemplo, o conteúdo protegido pode representar um token de redefinição de senha só deve ser válido por uma hora. Certamente é possível para o desenvolvedor criar seu próprio formato de carga que contém a data de validade inserida e desenvolvedores avançados talvez queiram fazer isso de qualquer forma, mas para a maioria dos desenvolvedores gerenciar esses expirações pode crescer entediante.

Para facilitar essa tarefa para o nosso público de desenvolvedores, o pacote [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contém APIs do utilitário para criar cargas que expiram automaticamente após um período de tempo definido. Essas APIs de suspensão do `ITimeLimitedDataProtector` tipo.

## <a name="api-usage"></a>Uso da API

O `ITimeLimitedDataProtector` interface é a interface principal para proteger e Desproteger cargas de tempo limitado / expiração automática. Para criar uma instância de um `ITimeLimitedDataProtector`, você precisará de uma instância de uma expressão [IDataProtector](xref:security/data-protection/consumer-apis/overview) construído com uma finalidade específica. Uma vez a `IDataProtector` instância está disponível, chame o `IDataProtector.ToTimeLimitedDataProtector` método de extensão para retornar um protetor com recursos internos de expiração.

`ITimeLimitedDataProtector` expõe os seguintes métodos de extensão e de superfície de API:

* CreateProtector (objetivo de cadeia de caracteres): ITimeLimitedDataProtector - esta API é semelhante à existente `IDataProtectionProvider.CreateProtector` que pode ser usado para criar [cadeias de propósito](xref:security/data-protection/consumer-apis/purpose-strings) de um protetor de tempo limitado de raiz.

* Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]

* Protect(byte[] plaintext, TimeSpan lifetime) : byte[]

* Proteger (byte [] em texto sem formatação): byte]

* Proteger (cadeia de caracteres em texto sem formatação, expiração de DateTimeOffset): cadeia de caracteres

* Proteger (texto sem formatação de cadeia de caracteres, tempo de vida de TimeSpan): cadeia de caracteres

* Proteger (cadeia de caracteres em texto sem formatação): cadeia de caracteres

Além das principais `Protect` métodos que levam apenas o texto não criptografado, há novas sobrecargas que permitem especificar a data de expiração do conteúdo. A data de validade pode ser especificada como uma data absoluta (por meio de um `DateTimeOffset`) ou como uma hora relativa (do sistema atual de tempo, por meio de um `TimeSpan`). Se uma sobrecarga que não tem uma expiração for chamada, a carga é considerada nunca para expirar.

* Desproteger (byte [] protectedData, out expiração DateTimeOffset): byte]

* Unprotect(byte[] protectedData) : byte[]

* Desproteger (cadeia de caracteres protectedData, out expiração DateTimeOffset): cadeia de caracteres

* Desproteger (cadeia de caracteres protectedData): cadeia de caracteres

O `Unprotect` métodos retornam os dados desprotegidos originais. Se a carga ainda não tiver expirado, a expiração absoluta é retornada como um parâmetro junto com os dados desprotegidos originais de saída opcional. Se o conteúdo tiver expirado, todas as sobrecargas do método Unprotect lançará CryptographicException.

>[!WARNING]
> Não é recomendável para usar essas APIs para proteger as cargas que exigem persistência de longo prazo ou indefinida. "Posso pagar para as cargas protegidas ser irrecuperáveis permanentemente após um mês?" pode servir como uma boa regra prática; Se a resposta for nenhum desenvolvedor, em seguida, deve considerar APIs alternativas.

O exemplo abaixo usa o [caminhos de código não-DI](xref:security/data-protection/configuration/non-di-scenarios) para instanciar o sistema de proteção de dados. Para executar este exemplo, certifique-se de que você adicionou uma referência ao pacote Microsoft.AspNetCore.DataProtection.Extensions pela primeira vez.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
