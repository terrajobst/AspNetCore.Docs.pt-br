O código do banco de dados de identidade gerado requer [migrações Entity Framework Core](/ef/core/managing-schemas/migrations/). Criar uma migração e atualizar o banco de dados. Por exemplo, execute os seguintes comandos:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

No **console do Gerenciador de pacotes**do Visual Studio:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

O parâmetro de nome "CreateIdentitySchema" para o comando `Add-Migration` é arbitrário. `"CreateIdentitySchema"` descreve a migração.
