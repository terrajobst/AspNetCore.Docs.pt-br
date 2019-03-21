---
ms.openlocfilehash: 698a127120f7f52672ceb927ff24eb1b02f91521
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320268"
---
<span data-ttu-id="be223-101">Exige que o código de banco de dados de identidade gerado [migrações do Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="be223-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="be223-102">Criar uma migração e atualizar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be223-102">Create a migration and update the database.</span></span> <span data-ttu-id="be223-103">Por exemplo, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="be223-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be223-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be223-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="be223-105">No Visual Studio **Package Manager Console**:</span><span class="sxs-lookup"><span data-stu-id="be223-105">In the Visual Studio **Package Manager Console**:</span></span>

```PMC
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="be223-106">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="be223-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="be223-107">O parâmetro de nome "CreateIdentitySchema" para o `Add-Migration` comando é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="be223-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="be223-108">`"CreateIdentitySchema"` Descreve a migração.</span><span class="sxs-lookup"><span data-stu-id="be223-108">`"CreateIdentitySchema"` describes the migration.</span></span>
