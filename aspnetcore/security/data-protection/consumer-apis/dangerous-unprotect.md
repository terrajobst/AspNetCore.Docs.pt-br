---
title: Desproteja as cargas cujas chaves foram revogadas no ASP.NET Core
author: rick-anderson
description: Saiba como desproteger dados, protegidos com chaves que foram revogadas desde então, em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666596"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Desproteja as cargas cujas chaves foram revogadas no ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

O ASP.NET Core APIs de proteção de dados não se destina principalmente à persistência indefinida de cargas confidenciais. Outras tecnologias como o [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) e o [Azure Rights Management](/rights-management/) são mais adequadas para o cenário de armazenamento indefinido e têm recursos de gerenciamento de chaves de alta segurança. Dito isso, não há nada proibindo um desenvolvedor de usar o ASP.NET Core APIs de proteção de dados para proteção de longo prazo de dados confidenciais. As chaves nunca são removidas do anel de chave, portanto `IDataProtector.Unprotect` sempre pode recuperar cargas existentes, desde que as chaves estejam disponíveis e válidas.

No entanto, surge um problema quando o desenvolvedor tenta desproteger dados que foram protegidos com uma chave revogada, pois `IDataProtector.Unprotect` gerará uma exceção nesse caso. Isso pode ser adequado para cargas de curta duração ou transitórias (como tokens de autenticação), pois esses tipos de cargas podem ser facilmente recriados pelo sistema e, no pior, o visitante do site pode precisar fazer logon novamente. Mas, para as cargas persistentes, ter `Unprotect` throw pode levar a uma perda de dados inaceitável.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Para dar suporte ao cenário de permitir que as cargas sejam desprotegidas mesmo diante de chaves revogadas, o sistema de proteção de dados contém um tipo de `IPersistedDataProtector`. Para obter uma instância de `IPersistedDataProtector`, basta obter uma instância de `IDataProtector` da maneira normal e tentar converter a `IDataProtector` em `IPersistedDataProtector`.

> [!NOTE]
> Nem todas as instâncias de `IDataProtector` podem ser convertidas em `IPersistedDataProtector`. Os desenvolvedores devem usar C# o operador as ou semelhante para evitar exceções de tempo de execução causadas por conversões inválidas e devem estar preparados para lidar adequadamente com o caso de falha.

`IPersistedDataProtector` expõe a seguinte superfície de API:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Essa API usa a carga protegida (como uma matriz de bytes) e retorna a carga não protegida. Não há sobrecarga baseada em cadeia de caracteres. Os dois parâmetros de saída são os seguintes.

* `requiresMigration`: será definido como true se a chave usada para proteger essa carga não for mais a chave padrão ativa, por exemplo, a chave usada para proteger essa carga é antiga e uma operação sem interrupção de chave desde que ocorreu. O chamador pode desejar considerar a nova proteção da carga, dependendo de suas necessidades comerciais.

* `wasRevoked`: será definido como true se a chave usada para proteger essa carga tiver sido revogada.

>[!WARNING]
> Tenha muito cuidado ao passar `ignoreRevocationErrors: true` para o método `DangerousUnprotect`. Se, depois de chamar esse método, o valor de `wasRevoked` for true, a chave usada para proteger esse conteúdo foi revogada e a autenticidade da carga deverá ser tratada como suspeita. Nesse caso, só continue a operar na carga desprotegida se você tiver alguma garantia separada de que ela é autêntica, por exemplo, que ela é proveniente de um banco de dados seguro em vez de ser enviada por um cliente Web não confiável.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
