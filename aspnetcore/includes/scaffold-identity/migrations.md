---
ms.openlocfilehash: 698a127120f7f52672ceb927ff24eb1b02f91521
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320268"
---
Exige que o código de banco de dados de identidade gerado [migrações do Entity Framework Core](/ef/core/managing-schemas/migrations/). Criar uma migração e atualizar o banco de dados. Por exemplo, execute os seguintes comandos:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No Visual Studio **Package Manager Console**:

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

O parâmetro de nome "CreateIdentitySchema" para o `Add-Migration` comando é arbitrário. `"CreateIdentitySchema"` Descreve a migração.
