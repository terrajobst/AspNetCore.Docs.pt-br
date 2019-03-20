---
title: Desproteger cargas cujas chaves foram revogadas no ASP.NET Core
author: rick-anderson
description: Saiba como Desproteger dados, protegidos com chaves que já tem sido revogadas, em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208025"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Desproteger cargas cujas chaves foram revogadas no ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

As APIs de proteção de dados do ASP.NET Core não destinam principalmente para persistência indefinida de cargas confidenciais. Outras tecnologias, como [DPAPI do Windows CNG](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) e [do Azure Rights Management](/rights-management/) são mais adequados para o cenário de armazenamento indefinido, e eles têm recursos de gerenciamento de chave forte de forma correspondente. Dito isso, não há nada proibindo um desenvolvedor que usa as APIs de proteção de dados do ASP.NET Core para proteção de longo prazo de dados confidenciais. As chaves nunca são removidas do anel de chave, portanto, `IDataProtector.Unprotect` sempre pode recuperar conteúdos existentes desde que as chaves estão disponíveis e válido.

No entanto, um problema surge quando o desenvolvedor tenta desproteger os dados que foi protegidos com uma chave revogada, como `IDataProtector.Unprotect` lançará uma exceção, nesse caso. Isso pode ser adequado para cargas de curta duração ou transitórias (como tokens de autenticação), pois esses tipos de cargas podem ser facilmente recriados pelo sistema e, na pior das hipóteses, o visitante do site pode ser necessário fazer logon novamente. Mas para cargas persistentes, tendo `Unprotect` throw pode levar à perda de dados inaceitável.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Para suportar o cenário de permitir que os conteúdos a serem desprotegidos mesmo diante de chaves revogadas, o sistema de proteção de dados contém um `IPersistedDataProtector` tipo. Para obter uma instância de `IPersistedDataProtector`, simplesmente obtém uma instância do `IDataProtector` no modo normal e tente conversão a `IDataProtector` para `IPersistedDataProtector`.

> [!NOTE]
> Nem todos os `IDataProtector` instâncias podem ser convertidas em `IPersistedDataProtector`. Os desenvolvedores devem usar o C# operador ou semelhante ou evitar exceções de tempo de execução causado por conversões inválidas, e eles devem estar preparados para tratar o caso de falha de forma adequada.

`IPersistedDataProtector` expõe a superfície de API a seguir:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Essa API usa o conteúdo protegido (como uma matriz de bytes) e retorna a carga desprotegida. Não há nenhuma sobrecarga baseada em cadeia de caracteres. Os dois parâmetros de saída são da seguinte maneira.

* `requiresMigration`: será ser definido como true se a chave usada para proteger esse conteúdo não é mais a chave padrão do Active Directory, por exemplo, a chave usada para proteger essa carga é antiga e uma chave sem interrupção operação tem desde ocorrido. O chamador poderá considerar o proteger novamente a carga, dependendo de suas necessidades de negócios.

* `wasRevoked`: será definido como true se a chave usada para proteger este conteúdo foi revogada.

>[!WARNING]
> Tenha bastante cuidado ao passar `ignoreRevocationErrors: true` para o `DangerousUnprotect` método. Se, depois de chamar esse método o `wasRevoked` valor for true, em seguida, a chave usada para proteger este conteúdo foi revogada e autenticidade do conteúdo deve ser tratada como suspeito. Nesse caso, apenas continue a operar na carga de desprotegidos se você tiver alguma garantia separada que é autêntico, por exemplo, se ele é proveniente de um banco de dados seguro em vez de que está sendo enviada por um cliente da web não confiável.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
