<a name="dc"></a>

<span data-ttu-id="0cf17-101">Adicione a seguinte classe `MvcMovieContext` à pasta *Models*:</span><span class="sxs-lookup"><span data-stu-id="0cf17-101">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="0cf17-102">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="0cf17-102">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="0cf17-103">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="0cf17-103">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="0cf17-104">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cf17-104">Add a database connection string</span></span>

<span data-ttu-id="0cf17-105">Adicione uma cadeia de conexão ao arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0cf17-105">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="0cf17-106">Adicionar os pacotes NuGet necessários</span><span class="sxs-lookup"><span data-stu-id="0cf17-106">Add required NuGet packages</span></span>

<span data-ttu-id="0cf17-107">Execute o seguinte comando da CLI do .NET Core para adicionar o SQLite e o CodeGeneration.Design ao projeto:</span><span class="sxs-lookup"><span data-stu-id="0cf17-107">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="0cf17-108">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="0cf17-108">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="0cf17-109">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="0cf17-109">Register the database context</span></span>

<span data-ttu-id="0cf17-110">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0cf17-110">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="0cf17-111">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0cf17-111">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="0cf17-112">Compile o projeto como uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="0cf17-112">Build the project as a check for errors.</span></span>