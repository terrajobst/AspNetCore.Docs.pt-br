<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="41a7b-101">Adicionar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="41a7b-101">Add a database context class</span></span>

<span data-ttu-id="41a7b-102">No projeto RazorPagesMovie, crie uma pasta chamada *Dados*.</span><span class="sxs-lookup"><span data-stu-id="41a7b-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="41a7b-103">Adicione a seguinte classe `RazorPagesMovieContext` à pasta *Dados*:</span><span class="sxs-lookup"><span data-stu-id="41a7b-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="41a7b-104">O código anterior cria uma propriedade `DbSet` para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="41a7b-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="41a7b-105">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="41a7b-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="41a7b-106">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="41a7b-106">Add a database connection string</span></span>

<span data-ttu-id="41a7b-107">Adicione uma cadeia de conexão ao arquivo *appsettings.json*, conforme mostrado no seguinte código destacado:</span><span class="sxs-lookup"><span data-stu-id="41a7b-107">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="41a7b-108">Adicionar pacotes NuGet e ferramentas de EF</span><span class="sxs-lookup"><span data-stu-id="41a7b-108">Add NuGet packages and EF tools</span></span>

<span data-ttu-id="41a7b-109">Abra um terminal para o projeto RazorPagesMovie.</span><span class="sxs-lookup"><span data-stu-id="41a7b-109">Open a terminal for the RazorPagesMovie project.</span></span>  <span data-ttu-id="41a7b-110">Clique com o botão direito do mouse no nome do projeto na barra de design/layout e vá para **Ferramentas > Abrir** no Terminal.</span><span class="sxs-lookup"><span data-stu-id="41a7b-110">Right click the project name in the design/layout bar and go to **Tools > Open** in Terminal.</span></span> <span data-ttu-id="41a7b-111">Execute os seguintes comandos da CLI do .NET Core no Terminal:</span><span class="sxs-lookup"><span data-stu-id="41a7b-111">Run the following .NET Core CLI commands in the Termial:</span></span>

```console
dotnet tool install --global dotnet-ef --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SQLite --version 3.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
```

<span data-ttu-id="41a7b-112">Os comandos anteriores adicionam Ferramentas do Entity Framework Core para a CLI do .NET e vários pacotes ao projeto.</span><span class="sxs-lookup"><span data-stu-id="41a7b-112">The preceding commands add Entity Framework Core Tools for the .NET CLI and several packages to the project.</span></span> <span data-ttu-id="41a7b-113">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="41a7b-113">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="41a7b-114">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="41a7b-114">Register the database context</span></span>

<span data-ttu-id="41a7b-115">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="41a7b-115">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="41a7b-116">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="41a7b-116">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="41a7b-117">Adicionar os pacotes NuGet necessários</span><span class="sxs-lookup"><span data-stu-id="41a7b-117">Add required NuGet packages</span></span>

<span data-ttu-id="41a7b-118">Execute o seguinte comando da CLI do .NET Core para adicionar o SQLite e o CodeGeneration.Design ao projeto:</span><span class="sxs-lookup"><span data-stu-id="41a7b-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

<span data-ttu-id="41a7b-119">O pacote `Microsoft.VisualStudio.Web.CodeGeneration.Design` é necessário para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="41a7b-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="41a7b-120">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="41a7b-120">Register the database context</span></span>

<span data-ttu-id="41a7b-121">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="41a7b-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="41a7b-122">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="41a7b-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="41a7b-123">Compile o projeto como uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="41a7b-123">Build the project as a check for errors.</span></span>
::: moniker-end
