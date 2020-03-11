<span data-ttu-id="c02ec-101">O código do banco de dados de identidade gerado requer [migrações Entity Framework Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="c02ec-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="c02ec-102">Criar uma migração e atualizar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c02ec-102">Create a migration and update the database.</span></span> <span data-ttu-id="c02ec-103">Por exemplo, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c02ec-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c02ec-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c02ec-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c02ec-105">No **console do Gerenciador de pacotes**do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c02ec-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c02ec-106">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="c02ec-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="c02ec-107">O parâmetro de nome "CreateIdentitySchema" para o comando `Add-Migration` é arbitrário.</span><span class="sxs-lookup"><span data-stu-id="c02ec-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="c02ec-108">`"CreateIdentitySchema"` descreve a migração.</span><span class="sxs-lookup"><span data-stu-id="c02ec-108">`"CreateIdentitySchema"` describes the migration.</span></span>
