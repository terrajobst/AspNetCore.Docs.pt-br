Execute os seguintes comandos da CLI do .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Os comandos anteriores adicionam:

* The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* The Entity Framework Core Tools for the .NET Core CLI.
* O provedor do EF Core SQLite, que instala o pacote EF Core como uma dependência.
* Pacotes necessários para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.

For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.
