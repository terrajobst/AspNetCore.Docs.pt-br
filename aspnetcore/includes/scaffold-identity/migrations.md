<span data-ttu-id="a33ff-101">O código do banco de dados de identidade gerado requer [migrações Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="a33ff-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="a33ff-102">Criar uma migração e atualizar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a33ff-102">Create a migration and update the database.</span></span> <span data-ttu-id="a33ff-103">Por exemplo, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a33ff-103">For example, run the following commands:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a33ff-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a33ff-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a33ff-105">No Visual Studio **Package Manager Console**:</span><span class="sxs-lookup"><span data-stu-id="a33ff-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a33ff-106">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a33ff-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="a33ff-107">O parâmetro de nome "CreateIdentitySchema" para o comando `Add-Migration` é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="a33ff-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="a33ff-108">`"CreateIdentitySchema"` descreve a migração.</span><span class="sxs-lookup"><span data-stu-id="a33ff-108">`"CreateIdentitySchema"` describes the migration.</span></span>
