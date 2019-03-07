<a name="dc"></a>
### 

Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Models*:  

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma propriedade `DbSet` para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Adicionar uma cadeia de conexão de banco de dados

Adicione uma cadeia de conexão ao arquivo *appsettings.json*:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Adicionar os pacotes NuGet necessários

Execute o seguinte comando da CLI do .NET Core para adicionar o SQLite e o CodeGeneration.Design ao projeto:

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrar o contexto de banco de dados

Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Compile o projeto como uma verificação de erros.
