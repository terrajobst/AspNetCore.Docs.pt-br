---
title: Adicione um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicione um modelo a um aplicativo ASP.NET Core simples.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: adf313418e82cc265304262f7a751273fa0e139f
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75952103"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="6c1bd-103">Adicione um modelo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="6c1bd-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="6c1bd-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="6c1bd-105">Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="6c1bd-106">Essas classes serão a parte “**M**odel” parte do aplicativo **M**VC.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="6c1bd-107">Você usa essas classes com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="6c1bd-108">O EF Core é uma estrutura ORM (de mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="6c1bd-109">As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o**bjetos **C**L**R** **b**ásicos) porque elas não têm nenhuma dependência no EF Core.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="6c1bd-110">Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="6c1bd-111">Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="6c1bd-112">Uma abordagem alternativa não abordada aqui é gerar classes de modelo de um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="6c1bd-113">Para obter informações sobre essa abordagem, consulte [ASP.NET Core – Banco de dados existente](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="6c1bd-114">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="6c1bd-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-116">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="6c1bd-117">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c1bd-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c1bd-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6c1bd-119">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c1bd-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c1bd-121">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar** > **nova classe** > **classe vazia**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-121">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="6c1bd-122">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-122">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="6c1bd-123">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-123">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="6c1bd-124">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-124">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="6c1bd-125">O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) em `ReleaseDate` especifica o tipo de dados (`Date`).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-125">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="6c1bd-126">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-126">With this attribute:</span></span>

* <span data-ttu-id="6c1bd-127">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-127">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="6c1bd-128">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-128">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="6c1bd-129">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-129">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="6c1bd-130">Adicionar pacotes do NuGet</span><span class="sxs-lookup"><span data-stu-id="6c1bd-130">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-132">No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** > **Package Manager Console** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-132">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6c1bd-134">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-134">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="6c1bd-135">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-135">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="6c1bd-136">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-136">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="6c1bd-137">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-137">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c1bd-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c1bd-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c1bd-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c1bd-140">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-140">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="6c1bd-141">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-141">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="6c1bd-142">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="6c1bd-142">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="6c1bd-144">A caixa de diálogo **selecionar projetos** será exibida, com o projeto `MvcMovie` selecionado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-144">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="6c1bd-145">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="6c1bd-145">Press the **Ok** button.</span></span>

<span data-ttu-id="6c1bd-146">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-146">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="6c1bd-147">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="6c1bd-147">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="6c1bd-148">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-148">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="6c1bd-149">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6c1bd-149">Create a database context class</span></span>

<span data-ttu-id="6c1bd-150">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="6c1bd-151">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="6c1bd-152">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-152">Create a *Data* folder.</span></span>

<span data-ttu-id="6c1bd-153">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="6c1bd-154">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6c1bd-155">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6c1bd-156">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="6c1bd-157">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="6c1bd-157">Register the database context</span></span>

<span data-ttu-id="6c1bd-158">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6c1bd-159">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="6c1bd-160">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6c1bd-161">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="6c1bd-162">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="6c1bd-163">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="6c1bd-164">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-166">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="6c1bd-167">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6c1bd-168">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="6c1bd-169">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6c1bd-169">Add a database connection string</span></span>

<span data-ttu-id="6c1bd-170">Adicione uma cadeia de conexão ao arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-172">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="6c1bd-173">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="6c1bd-174">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="6c1bd-174">Scaffold movie pages</span></span>

<span data-ttu-id="6c1bd-175">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-177">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* **> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="6c1bd-179">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="6c1bd-181">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="6c1bd-182">**Classe de modelo:** *filme (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="6c1bd-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="6c1bd-183">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="6c1bd-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc3.png)

* <span data-ttu-id="6c1bd-185">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="6c1bd-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="6c1bd-186">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="6c1bd-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="6c1bd-187">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="6c1bd-187">Select **Add**</span></span>

<span data-ttu-id="6c1bd-188">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-188">Visual Studio creates:</span></span>

* <span data-ttu-id="6c1bd-189">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="6c1bd-190">Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="6c1bd-191">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c1bd-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c1bd-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="6c1bd-193">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6c1bd-194">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="6c1bd-195">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c1bd-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c1bd-197">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="6c1bd-198">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="6c1bd-199">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="6c1bd-200">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="6c1bd-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="6c1bd-201">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="6c1bd-201">Initial migration</span></span>

<span data-ttu-id="6c1bd-202">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="6c1bd-203">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-205">No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** > **Package Manager Console** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="6c1bd-206">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="6c1bd-207">`Add-Migration InitialCreate`: gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6c1bd-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6c1bd-208">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6c1bd-209">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6c1bd-210">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6c1bd-211">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="6c1bd-212">`Update-Database`: atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6c1bd-213">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="6c1bd-214">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="6c1bd-215">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6c1bd-216">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6c1bd-217">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="6c1bd-218">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-219">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6c1bd-220">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="6c1bd-221">`ef migrations add InitialCreate`: gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="6c1bd-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="6c1bd-222">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6c1bd-223">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="6c1bd-224">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="6c1bd-225">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="6c1bd-226">`ef database update`: atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="6c1bd-227">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="6c1bd-228">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="6c1bd-228">The InitialCreate class</span></span>

<span data-ttu-id="6c1bd-229">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="6c1bd-230">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="6c1bd-231">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="6c1bd-232">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6c1bd-232">Test the app</span></span>

* <span data-ttu-id="6c1bd-233">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="6c1bd-234">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-236">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="6c1bd-237">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="6c1bd-238">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-238">Test the **Create** page.</span></span> <span data-ttu-id="6c1bd-239">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6c1bd-240">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6c1bd-241">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6c1bd-242">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-242">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6c1bd-243">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="6c1bd-244">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="6c1bd-244">Dependency injection in the controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-246">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6c1bd-247">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6c1bd-248">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6c1bd-250">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6c1bd-251">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="6c1bd-252">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="6c1bd-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="6c1bd-253">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="6c1bd-254">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="6c1bd-255">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="6c1bd-256">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="6c1bd-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="6c1bd-257">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="6c1bd-258">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="6c1bd-259">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="6c1bd-260">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="6c1bd-261">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="6c1bd-262">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="6c1bd-263">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="6c1bd-264">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="6c1bd-265">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="6c1bd-266">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="6c1bd-267">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="6c1bd-268">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="6c1bd-269">O parâmetro `id` é definido como um [tipo que permite valor nulo](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), caso um valor de ID não seja fornecido.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="6c1bd-270">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="6c1bd-271">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="6c1bd-272">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="6c1bd-273">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="6c1bd-274">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="6c1bd-275">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="6c1bd-276">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="6c1bd-277">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="6c1bd-278">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="6c1bd-279">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="6c1bd-280">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="6c1bd-281">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="6c1bd-282">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="6c1bd-283">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6c1bd-284">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="6c1bd-285">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="6c1bd-286">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c1bd-287">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6c1bd-287">Additional resources</span></span>

* [<span data-ttu-id="6c1bd-288">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="6c1bd-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6c1bd-289">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="6c1bd-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="6c1bd-290">[Anterior – Adicionando uma exibição](adding-view.md)
> [Próximo – Trabalhando com o SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="6c1bd-291">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="6c1bd-291">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-293">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="6c1bd-294">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-294">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-295">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-295">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6c1bd-296">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-296">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6c1bd-297">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="6c1bd-297">Scaffold the movie model</span></span>

<span data-ttu-id="6c1bd-298">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-298">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6c1bd-299">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-299">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-301">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* **> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-301">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="6c1bd-303">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-303">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="6c1bd-305">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-305">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="6c1bd-306">**Classe de modelo:** *filme (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="6c1bd-306">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="6c1bd-307">**Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão</span><span class="sxs-lookup"><span data-stu-id="6c1bd-307">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adicionar contexto de dados](adding-model/_static/dc.png)

* <span data-ttu-id="6c1bd-309">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="6c1bd-309">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="6c1bd-310">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="6c1bd-310">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="6c1bd-311">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="6c1bd-311">Select **Add**</span></span>

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="6c1bd-313">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-313">Visual Studio creates:</span></span>

* <span data-ttu-id="6c1bd-314">Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-314">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="6c1bd-315">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-315">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="6c1bd-316">Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-316">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="6c1bd-317">A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-317">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c1bd-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c1bd-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="6c1bd-319">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-319">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6c1bd-320">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-320">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6c1bd-321">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-321">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="6c1bd-322">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-322">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c1bd-323">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-323">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c1bd-324">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-324">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6c1bd-325">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-325">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6c1bd-326">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-326">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="6c1bd-327">Se você executar o aplicativo e clicar no link **Filme do MVC**, receberá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-327">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-328">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-328">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-329">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-329">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="6c1bd-330">Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-330">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="6c1bd-331">As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-331">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="6c1bd-332">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="6c1bd-332">Initial migration</span></span>

<span data-ttu-id="6c1bd-333">Nesta seção, há estas tarefas:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-333">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="6c1bd-334">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-334">Add an initial migration.</span></span>
* <span data-ttu-id="6c1bd-335">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-335">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-336">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-336">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6c1bd-337">No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** > **Package Manager Console** (PMC).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-337">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="6c1bd-339">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-339">In the PMC, enter the following commands:</span></span>

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="6c1bd-340">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-340">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="6c1bd-341">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-341">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="6c1bd-342">O argumento `Initial` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-342">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="6c1bd-343">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-343">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="6c1bd-344">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-344">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="6c1bd-345">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-345">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-346">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-346">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="6c1bd-347">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-347">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="6c1bd-348">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-348">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="6c1bd-349">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-349">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="6c1bd-350">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-350">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6c1bd-351">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6c1bd-351">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6c1bd-352">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-352">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6c1bd-353">Os serviços (como o contexto de BD do EF Core) são registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-353">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="6c1bd-354">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-354">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6c1bd-355">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-355">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c1bd-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c1bd-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c1bd-357">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-357">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="6c1bd-358">Examine o seguinte método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-358">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6c1bd-359">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-359">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="6c1bd-360">O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-360">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="6c1bd-361">O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-361">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6c1bd-362">O contexto de dados especifica quais entidades são incluídas no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-362">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="6c1bd-363">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-363">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6c1bd-364">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-364">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6c1bd-365">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-365">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6c1bd-366">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-366">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6c1bd-367">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-367">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c1bd-368">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c1bd-368">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6c1bd-369">Você criou um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-369">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="6c1bd-370">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6c1bd-370">Test the app</span></span>

* <span data-ttu-id="6c1bd-371">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-371">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="6c1bd-372">Se você receber uma exceção de banco de dados semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-372">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="6c1bd-373">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-373">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="6c1bd-374">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-374">Test the **Create** link.</span></span> <span data-ttu-id="6c1bd-375">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-375">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6c1bd-376">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-376">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6c1bd-377">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-377">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6c1bd-378">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-378">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6c1bd-379">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-379">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6c1bd-380">Examine a classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-380">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="6c1bd-381">o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="6c1bd-381">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="6c1bd-382">`services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-382">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="6c1bd-383">`=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-383">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="6c1bd-384">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-384">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="6c1bd-385">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-385">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="6c1bd-386">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-386">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="6c1bd-387">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="6c1bd-387">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="6c1bd-388">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-388">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="6c1bd-389">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-389">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="6c1bd-390">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-390">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="6c1bd-391">Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-391">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="6c1bd-392">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-392">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="6c1bd-393">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-393">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="6c1bd-394">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-394">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="6c1bd-395">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-395">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="6c1bd-396">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-396">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="6c1bd-397">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-397">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="6c1bd-398">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="6c1bd-398">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="6c1bd-399">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-399">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="6c1bd-400">O parâmetro `id` é definido como um [tipo que permite valor nulo](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), caso um valor de ID não seja fornecido.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-400">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="6c1bd-401">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-401">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="6c1bd-402">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-402">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="6c1bd-403">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-403">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="6c1bd-404">Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-404">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="6c1bd-405">Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-405">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="6c1bd-406">Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-406">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6c1bd-407">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-407">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="6c1bd-408">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-408">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="6c1bd-409">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-409">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="6c1bd-410">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-410">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="6c1bd-411">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-411">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="6c1bd-412">Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-412">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="6c1bd-413">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-413">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="6c1bd-414">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-414">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="6c1bd-415">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6c1bd-415">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="6c1bd-416">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:</span><span class="sxs-lookup"><span data-stu-id="6c1bd-416">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6c1bd-417">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6c1bd-417">Additional resources</span></span>

* [<span data-ttu-id="6c1bd-418">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="6c1bd-418">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="6c1bd-419">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="6c1bd-419">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="6c1bd-420">[Anterior, adicionando uma exibição](adding-view.md)
> [próxima trabalhando com um banco de dados](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="6c1bd-420">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
