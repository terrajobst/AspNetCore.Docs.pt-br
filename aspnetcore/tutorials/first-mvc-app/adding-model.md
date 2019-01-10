---
title: Adicione um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicione um modelo a um aplicativo ASP.NET Core simples.
ms.author: riande
ms.date: 12/8/2017
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 630b4b0549a8549d9570d701fb1691310ec442c3
ms.sourcegitcommit: 4e87712029de2aceb1cf2c52e9e3dda8195a5b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53381849"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="33993-103">Adicione um modelo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="33993-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="33993-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="33993-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="33993-105">Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="33993-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="33993-106">Essas classes serão a parte “**M**odel” parte do aplicativo **M**VC.</span><span class="sxs-lookup"><span data-stu-id="33993-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="33993-107">Você usa essas classes com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="33993-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="33993-108">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="33993-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="33993-109">As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o**bjetos **C**L**R** **b**ásicos) porque elas não têm nenhuma dependência no EF Core.</span><span class="sxs-lookup"><span data-stu-id="33993-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="33993-110">Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="33993-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="33993-111">Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="33993-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="33993-112">Uma abordagem alternativa não abordada aqui é gerar classes de modelo de um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="33993-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="33993-113">Para obter informações sobre essa abordagem, consulte [ASP.NET Core – Banco de dados existente](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="33993-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

## <a name="add-a-data-model-class"></a><span data-ttu-id="33993-114">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="33993-114">Add a data model class</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="33993-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33993-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33993-116">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="33993-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="33993-117">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="33993-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="33993-118">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="33993-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="33993-119">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="33993-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---  
<!-- End of VS tabs -->

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="33993-120">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="33993-120">Scaffold the movie model</span></span>

<span data-ttu-id="33993-121">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="33993-121">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="33993-122">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="33993-122">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="33993-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33993-123">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33993-124">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* **> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="33993-124">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="33993-126">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="33993-126">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="33993-128">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="33993-128">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="33993-129">**Classe de modelo:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="33993-129">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="33993-130">**Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão</span><span class="sxs-lookup"><span data-stu-id="33993-130">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adicionar contexto de dados](adding-model/_static/dc.png)

* <span data-ttu-id="33993-132">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="33993-132">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="33993-133">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="33993-133">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="33993-134">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="33993-134">Select **Add**</span></span>

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="33993-136">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="33993-136">Visual Studio creates:</span></span>

* <span data-ttu-id="33993-137">Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="33993-137">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="33993-138">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="33993-138">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="33993-139">Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (<em>Views/Movies/&ast;.cshtml</em>)</span><span class="sxs-lookup"><span data-stu-id="33993-139">Razor view files for Create, Delete, Details, Edit, and Index pages (<em>Views/Movies/&ast;.cshtml</em>)</span></span>

<span data-ttu-id="33993-140">A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="33993-140">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="33993-141">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="33993-141">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="33993-142">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="33993-142">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="33993-143">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="33993-143">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="33993-144">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="33993-144">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="33993-145">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="33993-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="33993-146">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="33993-146">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="33993-147">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="33993-147">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="33993-148">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="33993-148">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="33993-149">Se você executar o aplicativo e clicar no link **Filme do MVC**, receberá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="33993-149">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

<span data-ttu-id="33993-150">Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="33993-150">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="33993-151">As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="33993-151">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="33993-152">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="33993-152">Initial migration</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="33993-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33993-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33993-154">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="33993-154">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="33993-155">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="33993-155">Add an initial migration.</span></span>
* <span data-ttu-id="33993-156">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="33993-156">Update the database with the initial migration.</span></span>

<span data-ttu-id="33993-157">No menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet** > **Console do Gerenciador de pacotes**.</span><span class="sxs-lookup"><span data-stu-id="33993-157">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="33993-159">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="33993-159">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<span data-ttu-id="33993-160">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="33993-160">The `Add-Migration` command generates code to create the initial database schema.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="33993-161">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="33993-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]
<span data-ttu-id="33993-162">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="33993-162">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

---  
<!-- End of VS tabs -->

<span data-ttu-id="33993-163">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *Models/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="33993-163">The schema is based on the model specified in the `DbContext` (In the *Models/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="33993-164">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="33993-164">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="33993-165">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="33993-165">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="33993-166">O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="33993-166">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="33993-167">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="33993-167">The `Up` method creates the database.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="33993-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33993-168">Visual Studio</span></span>](#tab/visual-studio)

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="33993-169">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="33993-169">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="33993-170">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="33993-170">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="33993-171">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33993-171">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="33993-172">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="33993-172">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="33993-173">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="33993-173">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="33993-174">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="33993-174">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="33993-175">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="33993-175">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="33993-176">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="33993-176">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="33993-177">O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="33993-177">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="33993-178">O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="33993-178">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="33993-179">O contexto de dados especifica quais entidades são incluídas no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="33993-179">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="33993-180">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="33993-180">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="33993-181">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="33993-181">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="33993-182">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="33993-182">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="33993-183">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="33993-183">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="33993-184">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="33993-184">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="33993-185">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="33993-185">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="33993-186">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="33993-186">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="33993-187">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="33993-187">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="33993-188">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="33993-188">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="33993-189">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="33993-189">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="33993-190">Você criou um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="33993-190">You created a DB context and registered it with the dependency injection container.</span></span>

---

<span data-ttu-id="33993-191">O esquema é baseado no modelo especificado no `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="33993-191">The schema is based on the model specified in the `MvcMovieContext` (In the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="33993-192">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="33993-192">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="33993-193">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="33993-193">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="33993-194">Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="33993-194">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="33993-195">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="33993-195">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="33993-196">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="33993-196">Test the app</span></span>

* <span data-ttu-id="33993-197">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="33993-197">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="33993-198">Se você receber uma exceção de banco de dados semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="33993-198">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="33993-199">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="33993-199">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="33993-200">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="33993-200">Test the **Create** link.</span></span>

  > [!NOTE]
  > <span data-ttu-id="33993-201">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="33993-201">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="33993-202">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="33993-202">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="33993-203">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="33993-203">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="33993-204">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="33993-204">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="33993-205">Examine a classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="33993-205">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="33993-206">o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="33993-206">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="33993-207">`services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="33993-207">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="33993-208">`=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="33993-208">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="33993-209">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="33993-209">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="33993-210">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext `) no controlador.</span><span class="sxs-lookup"><span data-stu-id="33993-210">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext `) into the controller.</span></span> <span data-ttu-id="33993-211">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="33993-211">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="33993-212">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="33993-212">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="33993-213">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="33993-213">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="33993-214">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="33993-214">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="33993-215">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="33993-215">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="33993-216">Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="33993-216">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="33993-217">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.</span><span class="sxs-lookup"><span data-stu-id="33993-217">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="33993-218">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="33993-218">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="33993-219">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="33993-219">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="33993-220">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="33993-220">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="33993-221">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="33993-221">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="33993-222">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="33993-222">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="33993-223">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="33993-223">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="33993-224">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="33993-224">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="33993-225">O parâmetro `id` é definido como um [tipo que permite valor nulo](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), caso um valor de ID não seja fornecido.</span><span class="sxs-lookup"><span data-stu-id="33993-225">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="33993-226">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="33993-226">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="33993-227">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="33993-227">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="33993-228">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="33993-228">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="33993-229">Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição.</span><span class="sxs-lookup"><span data-stu-id="33993-229">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="33993-230">Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="33993-230">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="33993-231">Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="33993-231">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="33993-232">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="33993-232">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="33993-233">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="33993-233">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="33993-234">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="33993-234">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="33993-235">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="33993-235">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="33993-236">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="33993-236">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="33993-237">Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="33993-237">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="33993-238">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="33993-238">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="33993-239">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="33993-239">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="33993-240">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="33993-240">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="33993-241">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:</span><span class="sxs-lookup"><span data-stu-id="33993-241">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="33993-242">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="33993-242">Additional resources</span></span>

* [<span data-ttu-id="33993-243">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="33993-243">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="33993-244">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="33993-244">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="33993-245">[Anterior – Adicionando uma exibição](adding-view.md)
> [Próximo – Trabalhando com o SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="33993-245">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>  
