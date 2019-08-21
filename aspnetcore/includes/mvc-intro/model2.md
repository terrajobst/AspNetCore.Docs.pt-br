::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Crie uma pasta de *Dados*.

Adicione a seguinte classe `MvcMovieContext` à pasta *Dados*:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

O código anterior cria uma propriedade `DbSet` para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Adicionar uma cadeia de conexão de banco de dados

Adicione uma cadeia de conexão ao arquivo *appsettings.json*:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>Adicionar pacotes NuGet e ferramentas de EF

Execute os seguintes comandos da CLI do .NET Core:

```console
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

Os comandos anteriores adicionam Ferramentas do Entity Framework Core para a CLI do .NET e vários pacotes ao projeto. O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrar o contexto de banco de dados

Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Compile o projeto como uma verificação de erros do compilador.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Adicione a seguinte classe `MvcMovieContext` à pasta *Models*:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

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
```

O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrar o contexto de banco de dados

Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Compile o projeto como uma verificação de erros.
::: moniker-end