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
