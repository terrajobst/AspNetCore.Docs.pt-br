<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="b73d1-101">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="b73d1-101">Add a database context class</span></span>

<span data-ttu-id="b73d1-102">No projeto RazorPagesMovie, crie uma pasta chamada *Dados*.</span><span class="sxs-lookup"><span data-stu-id="b73d1-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="b73d1-103">Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados*:</span><span class="sxs-lookup"><span data-stu-id="b73d1-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="b73d1-104">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b73d1-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="b73d1-105">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="b73d1-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="b73d1-106">O código não será compilado até que as dependências sejam adicionadas em uma etapa posterior.</span><span class="sxs-lookup"><span data-stu-id="b73d1-106">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="b73d1-107">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="b73d1-107">Add a database connection string</span></span>

<span data-ttu-id="b73d1-108">Adicione uma cadeia de conexão ao arquivo *appsettings.json*, conforme mostrado no seguinte código destacado:</span><span class="sxs-lookup"><span data-stu-id="b73d1-108">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="b73d1-109">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="b73d1-109">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="b73d1-110">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="b73d1-110">Register the database context</span></span>

<span data-ttu-id="b73d1-111">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b73d1-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b73d1-112">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b73d1-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="b73d1-113">Adicionar os pacotes NuGet necessários</span><span class="sxs-lookup"><span data-stu-id="b73d1-113">Add required NuGet packages</span></span>

<span data-ttu-id="b73d1-114">Execute o seguinte comando CLI do .NET Core para adicionar SQLite e CodeGeneration. Design ao projeto:</span><span class="sxs-lookup"><span data-stu-id="b73d1-114">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="b73d1-115">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="b73d1-115">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="b73d1-116">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="b73d1-116">Register the database context</span></span>

<span data-ttu-id="b73d1-117">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b73d1-117">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b73d1-118">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b73d1-118">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="b73d1-119">Compile o projeto como uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="b73d1-119">Build the project as a check for errors.</span></span>

::: moniker-end
